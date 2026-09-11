# Case Study: Corosync Single-Link Dependency on a Legacy Network

## Situation

A two-node Proxmox VE cluster had been running its Corosync (cluster membership and quorum) traffic entirely over a legacy, untagged flat network left over from before the environment was segmented into VLANs. All administrative access (web UI, SSH) had already been migrated to a dedicated management VLAN, but the cluster communication link had not, leaving quorum dependent on a network that was undocumented, unsegmented, and not covered by current firewall policy review.

## Investigation

I planned a migration to add the management VLAN as a second, redundant Corosync link rather than doing a risky single-link cutover. Before touching the live link, I tested reachability between the two nodes over the target VLAN using the ports Corosync requires (UDP 5405-5406). The initial connectivity test failed one direction: one node's host-level firewall was not permitting inbound traffic from the other node's VLAN address for anything other than an unrelated monitoring service, so Corosync traffic on that path was being silently dropped rather than explicitly rejected. This meant that simply adding the VLAN as a link without checking firewall policy first would have looked configured correctly while actually leaving the cluster on a single point of failure.

## Root cause

The firewall gap existed because host-level rules had been written incrementally, service by service, without a review pass for the planned cluster-networking migration. The rule set allowed the specific traffic already in use but had no explicit allowance for Corosync on the new VLAN, so the migration would have failed silently rather than falling back safely.

## Corrective action

I added an explicit firewall rule permitting the required UDP ports between the two nodes' management-VLAN addresses, then re-tested reachability in both directions and confirmed it was clean before making any cluster-configuration change. I then added the management VLAN as a new, additional Corosync link rather than replacing the existing one, keeping the original flat-network link active as an automatic fallback. After applying the change, I verified on both nodes that both links showed a connected state and that cluster quorum remained continuously healthy throughout the entire migration, with no restart of cluster services and no loss of quorum at any point.

## Lessons

- A network segmentation project isn't finished until every dependent service, including low-level clustering protocols, is confirmed to be using the intended network, not just the services that are visible from the admin UI.
- Firewall policy built incrementally, service by service, needs a periodic review pass against planned network changes, since a working rule set for existing traffic can still silently block a new legitimate flow.
- Testing exact reachability on the exact ports a service needs, in both directions, before changing that service's configuration catches silent failures that would otherwise only surface during an actual outage.
- Adding a new link alongside an existing one, instead of cutting over directly, turns a risky single migration into a verifiable, reversible change with no downtime.
