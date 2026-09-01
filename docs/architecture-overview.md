# Architecture Overview

## Objective

I designed this lab as a maintainable, security-conscious home network and self-hosting environment. It separates device classes, hosts services on virtualized Linux workloads, and uses remote access, monitoring, and backups to support day-to-day operation and recovery.

## Logical design

```text
Internet
   |
Firewall / router
   |
VLAN-aware switch
 |       |        |
APs   Proxmox   Wired clients
           |
      VMs and LXCs
```

The firewall is the routing and policy-enforcement point between network zones. The switch carries tagged VLAN traffic to infrastructure links. Wireless access points map SSIDs to their intended client networks. Proxmox hosts attach workloads to the appropriate network rather than placing every service on a general-purpose LAN.

## Design priorities

- **Segmentation:** I separate management interfaces, household clients, smart-home devices, guests, and server workloads by trust level.
- **Least privilege:** I allow inter-VLAN access only when a user or service has a documented reason to communicate.
- **Administrative security:** Management interfaces are reachable from authorized internal networks or through WireGuard, rather than directly from the internet.
- **Resilience:** I distribute workloads across two Proxmox hosts where practical and keep monitoring and utility services separate from primary compute workloads.
- **Recoverability:** I retain local backups for fast restores and encrypted off-site copies for host-loss scenarios.
- **Documentation:** I record network decisions, deployments, service ownership, and incidents so changes can be repeated and failures can be investigated.

## Validation approach

I validate significant changes at the relevant layers: VLAN assignment, DHCP and DNS behavior, firewall reachability, application access, monitoring, and rollback or recovery readiness. This prevents a configuration that merely looks correct from becoming an untested dependency.
