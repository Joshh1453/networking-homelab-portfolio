# Backup and Recovery

## Strategy

I balance fast recovery from routine mistakes with recovery from host, disk, or local-storage loss.

| Backup layer | Purpose | Retention concept |
|---|---|---|
| Local Proxmox VZDump backups | Fast restoration after a failed update, accidental deletion, or configuration error | Keep recent restore points within local storage limits |
| Encrypted off-site backup copies | Recovery after loss of a host, boot drive, or local storage | Keep a longer retention window separate from the virtualization hosts |
| Application-level data and configuration | Preserve persistent data, exports, volumes, manifests, and service-specific settings | Document and validate per service |

I retain the latest two local guest backups and keep matching encrypted off-site archives for 30 days. Provider identifiers, storage-remote names, and recovery credentials are intentionally omitted from this public portfolio.

## Recovery principles

1. A backup is useful only if its restoration path is documented and tested.
2. Hypervisor backups do not automatically capture every external dependency, persistent mount, application export, or credential-recovery requirement.
3. Before re-creating a container or moving storage, I record persistent-data paths and configuration locations.
4. I verify that off-site archives are encrypted and that recovery material is stored separately from the systems being protected.
5. I maintain a written disaster-recovery process for host loss instead of relying on memory during an incident.

## Practical lesson

The environment uses host-local guest storage, so I do not represent it as shared-storage high availability. Recovery depends on backups, documented service dependencies, and deliberate restore validation rather than assuming a workload can automatically fail over to another host.