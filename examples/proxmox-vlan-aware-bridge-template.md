# Proxmox VLAN-Aware Bridge Template

This example shows a VLAN-aware Linux bridge for a Proxmox host. Interface names, addresses, gateways, and VLAN numbers are examples only.

```text
auto lo
iface lo inet loopback

auto eno1
iface eno1 inet manual

auto vmbr0
iface vmbr0 inet manual
    bridge-ports eno1
    bridge-stp off
    bridge-fd 0
    bridge-vlan-aware yes
    bridge-vids 10 20 30 40 60

auto vmbr0.10
iface vmbr0.10 inet static
    address 10.50.10.20/24
    gateway 10.50.10.1
```

## Design notes

- The physical uplink connects to a switch trunk that permits the required VLANs.
- `vmbr0` carries tagged guest traffic; the host management address is assigned to a dedicated VLAN subinterface.
- A guest's virtual NIC is assigned the appropriate VLAN tag in Proxmox.
- Restrict bridge VLANs and switch-trunk VLANs to the networks actually required.
- Validate host management reachability and guest connectivity after any bridge or switchport change.

## Example guest placement

| Guest role | Example VLAN | Reasoning |
|---|---:|---|
| Home-automation workload | 20 | Reachable from approved trusted clients |
| Lower-trust device integration | 30 | Subject to IoT boundary policy |
| General service workload | 60 | Separates server traffic from management and clients |
