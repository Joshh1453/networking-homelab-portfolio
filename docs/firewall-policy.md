# Firewall Policy Design

## Policy objective

I use the firewall to implement the network's trust boundaries. Rules are evaluated at the interface where traffic enters the firewall, so I organize policy around source zones and explicitly state what each zone may reach.

## High-level policy matrix

| Source zone | Allowed destinations | Default restriction |
|---|---|---|
| Management | Infrastructure and authorized internal services | Access is limited to known administrator devices and users |
| Trusted | Internet and approved service endpoints | No unrestricted access to management infrastructure |
| IoT | Required DNS, NTP, internet access, and narrowly approved automation endpoints | No lateral access to trusted, management, guest, or general server networks |
| Guest | Internet only | Deny all internal destinations and isolate guest clients where supported |
| Services | Required upstream services, updates, monitoring, and specific application dependencies | Deny unsolicited access to client and management networks |
| Remote VPN | Only routed networks and services required for administration | No implicit full-trust access beyond documented policy |

## Rule design practices

- I start each zone with a default-deny posture for internal destinations.
- I add narrow pass rules for a defined source, destination, protocol, and port when a requirement is identified.
- I use aliases or logical groups for recurring service destinations instead of duplicating addresses in many rules.
- I place DNS, DHCP, NTP, and monitoring dependencies deliberately; these are commonly missed during segmentation projects.
- I review logs for denied traffic while testing so necessary flows can be distinguished from overly broad access requests.
- I keep management-plane access distinct from application traffic.

## Example

A smart-home device may need DNS, time synchronization, outbound internet access for vendor connectivity, and access to one home-automation controller. That does not justify access to workstations, hypervisor management interfaces, or other IoT devices. I grant only those necessary flows.