# Wireless Deployment

## Objective

I designed the wireless environment to extend the same trust boundaries used on the wired network. Wireless clients are placed into the appropriate network zone based on their SSID, rather than treating Wi-Fi as one shared internal network.

## Wireless infrastructure

The wireless deployment uses five centrally managed TP-Link Omada access points. The design combines dedicated indoor coverage, outdoor coverage, a wall-mounted AP with wired-client bridging, and a mesh path for areas where a dedicated Ethernet run is not practical.

| Role | Model | Backhaul | Public-safe purpose |
|---|---|---|---|
| Outdoor coverage AP | TP-Link Omada EAP603-Outdoor | Wireless mesh | Extends centrally managed Wi-Fi coverage to an outdoor area |
| Additional indoor coverage AP | TP-Link Omada EAP650 | Wired PoE | Expands indoor wireless coverage and capacity |
| Wall-mounted AP / client bridge | TP-Link Omada EAP615-Wall | Wired PoE | Provides Wi-Fi and wired-client connectivity |
| Primary high-performance AP | TP-Link Omada EAP720 | Wired PoE | Provides primary managed wireless coverage |
| Mesh coverage-expansion AP | TP-Link Omada EAP720 | Wireless mesh | Extends coverage and provides the mesh path toward the outdoor AP |

Omada Controller runs as a virtualized workload and centrally manages AP adoption, WLAN configuration, mesh behavior, roaming settings, and client visibility. The portfolio intentionally omits live controller access information, device addresses, MAC addresses, physical placement, and switch-port assignments.

## Design pattern

| Wireless network type | VLAN mapping | Typical clients | Security intent |
|---|---|---|---|
| Trusted wireless | Trusted client VLAN | Personal devices and household clients | Controlled access to approved internal services |
| IoT wireless | IoT VLAN | Smart-home and lower-trust devices | Minimize lateral access and allow only required services |
| Guest wireless | Guest VLAN | Visitor devices | Internet-only access with guest-network isolation enabled |

## Wireless policy

- I use 5 GHz-preferred band steering to guide compatible clients toward higher-capacity spectrum.
- I enable Fast Roaming, AI Roaming, and Non-Stick Roaming to support client movement and reduce clients remaining associated with weak AP signals.
- I enable Omada Guest Network for the guest SSID. Guest isolation supplements firewall policy by restricting guest access to internal networks and reducing guest-to-guest communication.
- Wired PoE backhaul is preferred for primary coverage areas. The wireless mesh extends coverage where cabling is impractical, including the managed outdoor-coverage path.
- Trusted, IoT, and Guest SSIDs map to distinct VLANs with separate firewall policies.

## Switching and access points

I use VLAN-aware switch-to-access-point uplinks. The access-point management interface is placed in the management zone, while client VLANs are carried as tagged traffic over the uplink. This allows one wired AP connection to present multiple wireless networks without mixing their traffic.

For wired client connections, I use access ports assigned to the intended VLAN. For infrastructure links such as APs and hypervisors, I use a restricted VLAN trunk that carries only the VLANs needed by that device.

## Deployment and validation

- I configure the wired switchport and expected VLANs before adopting or moving an access point.
- I map each SSID to its intended client VLAN in the wireless controller.
- I verify AP adoption, management connectivity, client addressing, mesh status where applicable, and the expected client VLAN after provisioning.
- I test internet access, allowed internal-service access, and denied cross-zone access from each wireless network.
- I verify that Guest Network is enabled and use client-isolation testing to confirm guests retain internet access without reaching internal systems or other guest clients.

## Operational lesson

Wireless segmentation depends on more than SSID names. A successful design requires the correct switch trunk, AP backhaul, controller settings, DHCP behavior, firewall policy, and end-to-end testing. I validate each layer because a client can associate to an SSID while still receiving the wrong network, taking an unintended path, or being unable to reach required services.