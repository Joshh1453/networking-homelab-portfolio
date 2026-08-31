# My Networking & Homelab Portfolio

I built and operate this home infrastructure lab to develop practical experience in network design, firewall administration, virtualization, Linux operations, secure remote access, monitoring, and backup and recovery.

> **Privacy note:** I intentionally sanitize this public portfolio. Hostnames, public endpoints, internal addresses, SSIDs, physical locations, credentials, hardware identifiers, and other operational details have been replaced or omitted.

## What I built

- Segmented network zones for management, trusted clients, IoT, guests, and self-hosted services
- OPNsense-based routing and firewall policy with inter-VLAN access controls
- Cisco switching and TP-Link Omada wireless with VLAN-aware access and trunk links
- A two-node Proxmox VE environment hosting Linux containers, virtual machines, and administrative services
- WireGuard-based remote administration with no direct public exposure for management applications
- Monitoring, documented runbooks, local backups, encrypted off-site copies, and recovery procedures

## Portfolio guide

| Area | What it demonstrates |
|---|---|
| Architecture overview | My end-to-end infrastructure design and operational priorities |
| Network segmentation | VLANs, trust boundaries, and intended access between device classes |
| Firewall policy | Least-privilege design and inter-zone traffic control |
| Virtualization platform | Proxmox VE, Linux workloads, service placement, and resilience planning |
| Backup and recovery | Backup retention, encrypted off-site copies, and restoration planning |
| Case studies | My troubleshooting process, root-cause analysis, remediation, and validation |

## Technology used

OPNsense · Cisco IOS switching · TP-Link Omada · Proxmox VE · Debian/Ubuntu/Alpine Linux · WireGuard · Docker/Portainer · Uptime Kuma · rclone · Git/GitHub

## Operating principles

1. I segment devices by trust and purpose rather than treating the LAN as one flat network.
2. I keep administrative services off the public internet.
3. I document changes, validate them, and retain enough recovery information to rebuild after a failure.
4. I prefer simple, observable designs that I can realistically maintain in a home environment.