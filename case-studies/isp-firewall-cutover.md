# Case Study: ISP Router to Firewall Cutover

## Situation

I migrated the home network edge from an ISP-provided router to an OPNsense firewall. The objective was to move routing, DHCP, DNS, inter-VLAN routing, and firewall policy to a platform that supported segmentation and more explicit control.

## Plan

Before the cutover, I documented the target topology, required wired connections, management access, DHCP responsibilities, and a recovery path. I kept an administrator workstation connected to the new firewall's LAN side so that management access remained available during the WAN transition.

## Execution

I released the WAN lease from the ISP router, disconnected the old WAN connection, moved the provider handoff to the firewall WAN interface, and verified that the firewall received upstream connectivity.

## Validation

I tested the new edge in layers:

- WAN address acquisition and gateway reachability
- DNS resolution and outbound internet connectivity
- DHCP leases and correct gateway assignment for internal clients
- Inter-VLAN routing and firewall-policy behavior
- Management access to infrastructure devices and hosted services

## Lessons

- A documented physical cutover order reduces downtime and prevents accidental loss of management access.
- Keeping a client on the firewall LAN provides a reliable recovery path during WAN changes.
- Edge-router migration is successful only after both external connectivity and internal policy behavior are validated.