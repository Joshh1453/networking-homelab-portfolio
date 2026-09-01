# Virtualization Platform

## Platform summary

I run a two-node Proxmox VE environment on Debian-based hosts. The primary compute host runs home-automation, media, and game-server workloads. A secondary host carries monitoring, network-performance tracking, notifications, and network-management workloads, which keeps operational visibility less dependent on the primary compute host.

## Workload approach

| Workload type | Platform pattern | Reasoning |
|---|---|---|
| Lightweight Linux services | Unprivileged LXC containers where appropriate | Efficient resource use with straightforward lifecycle management |
| Applications with stronger isolation or appliance requirements | Virtual machines | Clearer operating-system boundaries and compatibility |
| Containerized management services | VM or LXC with Docker/Portainer where appropriate | Repeatable deployment and persistent-volume awareness |

## Networking

I use VLAN-aware Proxmox bridges. Each guest attaches to the VLAN appropriate for its purpose, which allows the firewall to enforce the same segmentation policy for self-hosted services as for physical devices.

I treat management connectivity separately from guest-service networking. This distinction matters during outages: a service can remain available even when I cannot reach a hypervisor management interface, and the reverse can also occur.

## Operational practices

- I enable automatic startup for services that should recover after a host reboot.
- I use deletion protection for important workloads where practical.
- I record persistent-data locations, service dependencies, and restoration requirements before migrations or major changes.
- I validate management connectivity, guest network placement, and service health after network changes.
- I treat two nodes as improved operational flexibility, not automatic high availability: host-local disks require explicit backup and recovery planning.

## Resilience lesson

Separating monitoring and utility services from primary application workloads improves diagnosis during a primary-host incident. However, it does not replace tested recovery procedures, configuration backups, or a documented dependency inventory.
