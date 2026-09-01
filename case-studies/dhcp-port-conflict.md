# Case Study: DHCP Service Port Conflict

## Situation

A planned DHCP service was unable to bind reliably on the firewall because another DHCP implementation was already listening on UDP port 67 for the affected interface.

## Investigation

I verified the intended DHCP implementation, checked service state, and identified that an older DHCP service remained active. Since only one process can own the relevant DHCP server socket, the intended service could not reliably start or serve leases.

## Root cause

Two DHCP services were configured to operate on overlapping interfaces. The legacy service retained the UDP port 67 listener, preventing the intended service from binding to it.

## Corrective action

I disabled the redundant service on the affected interface, confirmed the intended DHCP service could bind successfully, and validated client lease acquisition, gateway assignment, DNS resolution, and external connectivity.

## Lessons

- Migration from one infrastructure service to another requires explicit retirement of the old listener.
- Service status alone is not enough; socket ownership and client behavior must also be tested.
- DHCP validation should include a complete path: lease, gateway, DNS, and internet reachability.
