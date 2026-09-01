# Sanitized Network Topology

This diagram represents the portfolio's logical architecture. It intentionally omits real hostnames, addresses, endpoints, SSIDs, physical locations, hardware identifiers, and management paths.

```mermaid
flowchart LR
    Internet[Internet] --> FW[Firewall / Router]
    FW --> Core[VLAN-aware Switching]

    Core --> Mgmt[Management Zone]
    Core --> Trusted[Trusted Client Zone]
    Core --> IoT[IoT Zone]
    Core --> Guest[Guest Zone]
    Core --> Services[Service Zone]

    Core --> AP[Wireless Access Points]
    AP --> Trusted
    AP --> IoT
    AP --> Guest

    Core --> PVE1[Proxmox Host 1]
    Core --> PVE2[Proxmox Host 2]
    PVE1 --> Services
    PVE2 --> Services

    Remote[Remote Administrator] -. Encrypted VPN .-> FW

    classDef zone fill:#8250df,color:#ffffff,stroke:#6639ba;
    classDef infra fill:#1f6feb,color:#ffffff,stroke:#0d419d;
    classDef compute fill:#238636,color:#ffffff,stroke:#146c2e;
    classDef remote fill:#bf8700,color:#ffffff,stroke:#8c6500;
    class Mgmt,Trusted,IoT,Guest,Services zone;
    class FW,Core,AP infra;
    class PVE1,PVE2 compute;
    class Remote remote;
```

## Design notes

- The firewall routes traffic between zones and enforces inter-zone access policy.
- Management interfaces are separated from routine client and service traffic.
- Wireless SSIDs map clients into trusted, IoT, or guest zones.
- Proxmox guests are placed in the network zone that matches their role.
- Remote administration uses an authenticated VPN path rather than direct public access to management interfaces.
- Client, service, and management access is governed by least-privilege policy.