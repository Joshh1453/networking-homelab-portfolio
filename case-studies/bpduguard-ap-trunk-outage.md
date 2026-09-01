# Case Study: BPDU Guard on an Access-Point Trunk

## Situation

A Cisco switch disabled an access-point uplink after receiving Bridge Protocol Data Units on a port protected by BPDU Guard. This interrupted connectivity for the access point and its dependent clients.

## Investigation

I reviewed the switch event messages and confirmed that the interface had entered an error-disabled state because BPDU Guard detected BPDUs. I then examined the port's intended role: it was no longer a simple endpoint access port, but a Layer 2 infrastructure uplink carrying multiple VLANs.

## Root cause

PortFast and BPDU Guard were enabled as protective defaults for edge-access ports. Those defaults are appropriate for ordinary clients, but the uplink required infrastructure-aware switchport treatment. A connected device behind the AP also generated Layer 2 control traffic that triggered the protection.

## Corrective action

I corrected the switchport design to reflect its infrastructure role, limited its trunk VLANs to the required set, and applied spanning-tree protections appropriate to that port type. I restored the interface, verified AP connectivity, and tested client access on the expected wireless VLANs.

## Lessons

- Strong default protections must be reviewed when an access port becomes an infrastructure or trunk connection.
- A trunk should carry only the VLANs it requires.
- Switch logs are essential evidence when diagnosing an error-disabled interface.
- Changes to Layer 2 protections should be validated with both AP management and client-data-plane tests.
