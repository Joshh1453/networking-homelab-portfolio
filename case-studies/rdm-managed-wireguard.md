# Building RDM-Managed WireGuard Remote Access

## Overview

I designed and validated a Remote Desktop Manager (RDM)-managed WireGuard workflow for my segmented home lab. My goal was to start a VPN from within my remote-management workflow, then reach private infrastructure sessions without exposing management services directly to the Internet.

I removed or generalized all credentials, key material, endpoint information, public addresses, device names, and exact internal addressing from this public case study.

## What I Built

I built a remote-access workflow that:

- Starts an on-demand WireGuard tunnel from RDM on macOS
- Connects to an OPNsense WireGuard service
- Uses split tunneling so my normal Internet traffic stays local
- Reaches selected private management, services, VPN, and LAN networks
- Can later be integrated with RDM session and folder policies
- Uses a unique WireGuard identity instead of reusing another device profile

## Architecture

```text
macOS workstation
        |
        | Remote Desktop Manager launches WireGuard
        v
macOS WireGuard utun interface
        |
        | Encrypted UDP transport over the Internet
        v
OPNsense firewall / WireGuard service
        |
        +--> management VLAN
        +--> services VLAN
        +--> VPN client network
        +--> private LAN
```

I designed this as a split-tunnel connection. Only approved private routes enter the WireGuard tunnel, while general browsing continues through the workstation's current network connection.

## How I Configured It

I used two WireGuard identities with intentionally different responsibilities:

| Location | Required key material | Purpose |
|---|---|---|
| RDM WireGuard interface | Client private key | Identifies my macOS/RDM client; retained only in encrypted local RDM storage |
| OPNsense client peer | Client public key | Authorizes the individual RDM client and its unique tunnel address |
| RDM peer entry | OPNsense WireGuard instance public key | Identifies the remote WireGuard service |

Because my macOS workstation is the roaming client, I configured the public endpoint and UDP port on the RDM client peer. The matching OPNsense peer does not need a fixed endpoint for the client.

## Design Decisions

### Dedicated client identity

I created a dedicated WireGuard keypair and unique client address for the RDM-managed connection. This gives me clean revocation, prevents key reuse between applications or devices, and makes peer ownership clear when I troubleshoot the environment.

### Split-tunnel routing

I included only the private networks needed for my infrastructure in the client AllowedIPs list. This lets me reach management and service networks without unnecessarily routing all workstation traffic through my home network.

### RDM-controlled lifecycle

I used RDM as the VPN lifecycle tool for the workstation. RDM generates a temporary WireGuard configuration, invokes `wg-quick`, and creates a macOS `utun` interface. I can then link remote infrastructure sessions to the VPN entry through RDM's session-link policy model.

## Troubleshooting and Lessons

### 1. I used an invalid tunnel name

My first launch failed because I used a human-readable tunnel label containing spaces. RDM passed that value to `wg-quick`, which expects an interface-safe name or valid configuration filename.

**Fix:** I changed the tunnel name to a short lowercase value using safe interface characters:

```text
rdmwg
```

### 2. I learned that an interface is not proof of a working tunnel

After I corrected the name, macOS created a `utun` interface. That still did not prove that the VPN was usable. When I inspected the tunnel with `sudo wg show`, I initially saw interface information without a `peer:` block.

**Lesson:** A local WireGuard interface can exist even when no effective remote-peer configuration has been applied. I verify the peer, endpoint, handshake, and transfer counters instead of relying only on the RDM launch result.

### 3. I corrected reversed public-key placement

I initially configured the RDM peer with my client's own public key. A WireGuard peer must contain the *remote* side's public key.

**Fix:** I placed the OPNsense WireGuard instance public key in the RDM peer entry and the RDM client public key in the matching OPNsense peer.

### 4. I treated an existing interface as a state signal

When I tried to start an already-active tunnel again, RDM reported that the interface already existed as `utunX`.

**Lesson:** I treat that as an operational-state signal rather than immediately assuming a second failure. I verify the live interface before attempting another launch.

## How I Validated It

I validated the final configuration at three layers:

1. **Local WireGuard state**

   ```bash
   sudo wg show
   ```

   I looked for a configured peer, endpoint, approved routes, a recent handshake, non-zero send/receive counters, and persistent keepalive.

2. **Firewall-side peer status**

   I confirmed that the corresponding OPNsense WireGuard peer reported healthy after the authenticated handshake and traffic exchange.

3. **Application-level access**

   I opened a private infrastructure service through my RDM workflow. This confirmed that routing and firewall policy allowed useful traffic, not merely a successful handshake.

## Outcome

- I can launch and manage the WireGuard tunnel from RDM on macOS.
- My firewall authenticates the dedicated client peer.
- A current handshake and bidirectional encrypted traffic confirm tunnel health.
- Split-tunnel routing exposes only the private networks I intend to access.
- I can attach the design to RDM folders or sessions so private infrastructure connections start the VPN when needed.

## Security Practices

- I do not commit WireGuard private keys, pre-shared keys, exported `*.conf` files, credentials, or raw diagnostic logs.
- I keep endpoint addresses, DDNS names, public IP addresses, and exact internal topology out of public documentation.
- I use a unique WireGuard keypair and tunnel address for every device or client role.
- If a workstation is lost, compromised, or retired, I revoke its matching OPNsense peer immediately.
- I confirm that an active tunnel is stopped before I attempt another launch.

## Skills Demonstrated

- WireGuard peer and key-placement design
- OPNsense remote-access configuration
- Split-tunnel routing and segmented-network access
- macOS network diagnostics with `wg`
- RDM VPN lifecycle integration
- Root-cause analysis and layered troubleshooting
- Security-conscious infrastructure documentation

## Next Steps

- I will validate folder-level RDM session-link automation with multiple dependent sessions.
- I will confirm shared-VPN behavior across concurrent RDM sessions.
- I will decide the preferred VPN close behavior after final-session termination.
- I plan to create a sanitized architecture diagram showing the workstation, RDM, OPNsense, and abstract VLAN/service zones.
