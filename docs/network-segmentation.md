# Network Segmentation

## Purpose

A flat home LAN lets every device discover and reach every other device. I use VLANs to separate devices by trust level and function, then apply firewall policy at the boundaries.

## Zone model

| Zone | Typical devices | Intended access |
|---|---|---|
| Management | Firewall, switch, AP, hypervisor management interfaces | Administrators only; may manage infrastructure and approved services |
| Trusted | Personal computers, phones, game consoles, household devices | Internet access and controlled access to approved internal services |
| IoT | Smart-home devices and lower-trust embedded clients | Internet only when required; narrowly scoped access to home-automation services |
| Guest | Visitor devices | Internet-only access with client isolation |
| Homelab / services | Self-hosted applications, monitoring, and platform workloads | Reachable only from zones with an explicit service need |

## Implementation pattern

- The firewall has an interface for each zone and routes traffic between them.
- Switch-to-infrastructure uplinks use VLAN trunks; endpoint ports use the required access VLAN.
- Wireless SSIDs map to the trusted, IoT, and guest zones.
- Hypervisor bridges are VLAN-aware so I can place a VM or container into its intended security zone.
- I administratively disable unused switch ports or assign them to an isolated parking VLAN.

## Access philosophy

I keep the management network separate from ordinary client traffic. Trusted devices can access selected services such as home automation, media, and game workloads. I reduce IoT access to the minimum needed for operation, such as communication with a home-automation platform. Guest traffic is prevented from reaching internal networks.

## What I learned

Segmentation is not complete when VLANs exist. The design must also account for switch port mode, SSID mapping, DHCP placement, DNS behavior, firewall direction, return routing, and service-specific dependencies. Testing from a client in each zone is essential.
