# Case Study: Migrating Remote Administration from WireGuard to Tailscale

## Situation

I had a working remote-administration workflow based on a manually managed WireGuard tunnel integrated with Remote Desktop Manager (RDM). It provided encrypted access to selected internal resources, but it also required managing a VPN endpoint, dynamic-DNS dependency, client key material, split-tunnel route configuration, firewall rules, and an RDM-specific VPN launch sequence.

The goal was not simply to replace a working VPN. I wanted to simplify remote operations while preserving segmented-network access, least-privilege firewall enforcement, and a reliable recovery path.

## Investigation

I evaluated the active dependencies of the existing design: remote device authentication, encrypted transport, access to selected private networks, RDM session connectivity, and firewall controls. I separated the functions that were still valuable from the implementation details that created operational overhead.

The analysis showed that an identity-based overlay could replace the manually operated tunnel while a firewall-based subnet router could preserve access to existing private-address targets. That meant RDM could remain the session and credential-management tool, without being responsible for launching or monitoring a VPN connection for every remote session.

## Design

I deployed Tailscale as the active remote-access layer with two complementary access patterns:

- A firewall-based subnet router provides controlled access to approved private network segments for systems that cannot or should not run a client.
- Direct Tailscale clients on key virtualization hosts provide independent encrypted management paths for host-level administration and troubleshooting.

I retained existing firewall rules as the policy-enforcement layer. Tailscale determines the authenticated transport path; firewall policy still determines which internal destinations and protocols are permitted.

I also separated private homelab access from general Internet egress. Optional privacy-oriented browsing uses a managed exit-node service, while access to private infrastructure continues through the authenticated overlay and approved internal routes.

## Migration approach

I migrated in stages rather than removing the old path first:

1. Enrolled administrative devices and infrastructure hosts in the tailnet.
2. Configured and approved only the private routes needed for normal administration.
3. Tested direct host access and route-based access from an external network.
4. Confirmed RDM could continue using normal private target addresses without starting its former WireGuard profile.
5. Disabled the active WireGuard service, its related WAN exposure, and its dynamic-DNS dependency only after the replacement path was working.
6. Retained the former configuration in a disabled state as documented contingency material rather than deleting the recovery history.

## Validation

I validated the new design at multiple layers:

- Tailnet devices showed connected and authenticated status.
- Direct access to key virtualization hosts worked through their individual encrypted identities.
- Selected internal targets remained reachable through the firewall-based subnet router.
- RDM sessions connected using existing private addresses over the active Tailscale path.
- Remote access continued to work from outside the home network, including with privacy-oriented Internet egress enabled.
- The legacy remote-access configuration, related WAN rule, and dynamic-DNS updater were disabled only after validation succeeded.

## Outcome

The active remote-access workflow is now simpler to operate and less dependent on a manually managed public VPN endpoint. Administrative services remain private, RDM retains its role as a connection manager and credential vault, and firewall policy continues to enforce the boundaries between network zones.

The final design also improves resilience: direct host access gives me a separate management option while I troubleshoot routing or firewall behavior affecting private subnet access.

## Lessons

- A successful VPN migration should preserve security boundaries and operational recovery paths, not just replace one connection method with another.
- Separating encrypted transport from firewall authorization keeps the access model easier to reason about and audit.
- A subnet router can provide controlled access to existing private-address infrastructure without installing a client on every device.
- Direct host identities are useful for independent management and troubleshooting, but they should be applied selectively rather than becoming an unmanaged second network.
- Keeping a former remote-access design disabled and documented during transition is safer than deleting it before the new design has been tested from a real external network.

## Skills Demonstrated

- Tailscale mesh-network design and device identity management
- Firewall-based subnet routing and least-privilege access controls
- Remote Desktop Manager workflow migration
- Virtualization-host management and resilient control-plane access
- Secure remote-access migration planning, testing, and rollback awareness
- Security-conscious technical documentation
