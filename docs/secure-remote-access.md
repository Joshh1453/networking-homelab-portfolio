# Secure Remote Access

## Objective

I use Tailscale-based remote access to administer my segmented home lab without exposing administrative interfaces directly to the public Internet. The design provides an encrypted, identity-based path to approved internal resources while preserving existing firewall policy and network trust boundaries.

## Security model

```text
Remote administrator
        |
  Authenticated Tailscale overlay
        |
  Firewall-based subnet routing and policy enforcement
        |
Approved internal management and service resources
```

A firewall-based subnet router provides access to selected private networks for systems that do not run a Tailscale client. Direct Tailscale clients on key virtualization hosts provide an independent management path for resilience and troubleshooting.

## Design choices

- I do not expose firewall administration, hypervisor management, SSH, RDP, remote-management platforms, or internal service dashboards directly to the public Internet.
- I use Tailscale device identity and tailnet authorization before remote access is available.
- I use a subnet router for controlled access to approved private networks rather than installing a VPN client on every workload or network appliance.
- I retain firewall policy as the enforcement point for protocol- and destination-specific access; encrypted overlay connectivity does not bypass segmentation.
- I use direct Tailscale clients on key infrastructure hosts as an independent management path when diagnosing routing, VLAN, or firewall behavior.
- I keep local management and console access available as a final break-glass option.
- I can revoke a lost, replaced, or no-longer-trusted device through the tailnet administration console.

## Internet egress separation

Remote administration and privacy-oriented Internet egress are separate concerns. When I need privacy-oriented public browsing from an administrative device, I use a managed privacy exit node rather than routing general Internet traffic through the home lab. Private homelab access continues over approved Tailscale routes.

This separation keeps remote management focused on approved internal destinations and avoids making the home firewall or virtualization platform responsible for general-purpose Internet exit traffic.

## Migration and operations

I migrated from a manually managed WireGuard workflow that relied on a dedicated client configuration, dynamic DNS, and a firewall-hosted VPN service. The legacy configuration is retained in a disabled state as a documented contingency path, but Tailscale is the active transport for remote administration.

Remote Desktop Manager remains the session and credential-management layer. It connects to normal private target addresses after Tailscale is connected; it no longer needs to launch a WireGuard tunnel before starting each session.

## Operational validation

I validate remote access from an external network after material changes. Validation includes confirming tailnet connectivity, testing direct access to key infrastructure hosts, testing selected private resources through the subnet router, and reviewing firewall behavior for any failed flow.

## Sanitization note

This portfolio intentionally omits tailnet identifiers, device identities, internal address ranges, route advertisements, firewall rule identifiers, legacy endpoint details, authentication material, and operational screenshots. The detailed implementation and recovery procedures remain in private operational documentation.
