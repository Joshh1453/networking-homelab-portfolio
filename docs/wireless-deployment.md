# Wireless Deployment

## Objective

I designed the wireless environment to extend the same trust boundaries used on the wired network. Wireless clients are placed into the appropriate network zone based on their SSID, rather than treating Wi-Fi as one shared internal network.

## Design pattern

| Wireless network type | VLAN mapping | Typical clients | Security intent |
|---|---|---|---|
| Trusted wireless | Trusted client VLAN | Personal devices and household clients | Controlled access to approved internal services |
| IoT wireless | IoT VLAN | Smart-home and lower-trust devices | Minimize lateral access and allow only required services |
| Guest wireless | Guest VLAN | Visitor devices | Internet-only access with client isolation |

## Switching and access points

I use VLAN-aware switch-to-access-point uplinks. The access-point management interface is placed in the management zone, while client VLANs are carried as tagged traffic over the uplink. This allows one wired AP connection to present multiple wireless networks without mixing their traffic.

For wired client connections, I use access ports assigned to the intended VLAN. For infrastructure links such as APs and hypervisors, I use a restricted VLAN trunk that carries only the VLANs needed by that device.

## Deployment and validation

- I configure the wired switchport and expected VLANs before adopting or moving an access point.
- I map each SSID to its intended client VLAN in the wireless controller.
- I verify AP adoption, management connectivity, client addressing, and the expected client VLAN after provisioning.
- I test internet access, allowed internal-service access, and denied cross-zone access from each wireless network.
- I keep guest-client isolation enabled where supported.

## Operational lesson

Wireless segmentation depends on more than SSID names. A successful design requires the correct switch trunk, native or management VLAN behavior, controller settings, DHCP behavior, and firewall policy. I use end-to-end testing after changes because a client can associate to an SSID while still receiving the wrong network or being unable to reach required services.
