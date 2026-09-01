# Secure Remote Access

## Objective

I use WireGuard-based remote access for administration while away from the home network. The VPN creates an authenticated, encrypted path to approved internal resources instead of exposing management interfaces directly to the public internet.

## Security model

```text
Remote administrator
        |
  Encrypted WireGuard tunnel
        |
  Firewall policy enforcement
        |
Approved internal management and service networks
```

The firewall terminates the VPN and applies policy to VPN-originated traffic. Remote access is treated as its own source zone, so permitted routes and services can be limited to legitimate administration needs.

## Design choices

- I do not use direct WAN port forwards for firewall administration, hypervisor management, SSH, RDP, remote-management platforms, or internal service dashboards.
- I use WireGuard peer authentication before allowing access to administrative resources.
- I limit routed networks and firewall permissions to required management and service destinations.
- I retain the option to revoke an individual peer without changing the rest of the deployment.
- I validate return routing, DNS behavior, and firewall logs from an external client after making network changes.

## Operational considerations

VPN access is not a substitute for authorization policy. A successful VPN connection must still be subject to appropriate firewall rules, service authentication, and least-privilege routing. I also treat endpoint protection and private-key storage as part of the remote-access boundary.

## Sanitization note

This portfolio intentionally omits the live VPN endpoint, dynamic-DNS record, listening port, peer public keys, allowed-route values, and firewall rule identifiers. Those implementation details remain in the private operational documentation.