# Case Study: Proxmox Management Outage

## Situation

During a management-network migration, a power interruption caused both Proxmox hosts to become unreachable through their web management interfaces from the trusted client network. Guest workloads on service networks remained reachable, which narrowed the problem to the management path rather than a complete platform outage.

## Investigation

I separated the incident into layers: host availability, guest-service availability, routing, firewall policy, and hypervisor management access. The evidence showed that the hosts were online and workloads were still functioning, but return traffic for management sessions was not reaching the expected gateway.

## Root cause

Two conditions contributed to the outage:

- A host-level VPN client had altered return routing for trusted-network traffic.
- After restart, legacy host-level firewall restrictions did not match the migrated management-access design.

Together, these conditions prevented reliable return traffic and blocked expected management access even though the hosts and guest workloads remained online.

## Corrective action

I corrected the host-level routing behavior, reviewed the hypervisor firewall defaults, and aligned management-access policy with the intended network design. I then tested management reachability from the trusted network and confirmed guest-service connectivity remained intact.

## Lessons

- A reachable guest workload does not prove that hypervisor management is reachable.
- Host-level VPN clients can affect return routing in ways that firewall rules alone do not reveal.
- Network migrations should include post-reboot validation of routing and host-level firewall behavior.
- Maintaining a fallback management path and documenting rollback steps reduces recovery time.
