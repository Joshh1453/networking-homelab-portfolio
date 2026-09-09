# Isolating WAN Performance Degradation With a Direct-Handoff Test

## Summary

I investigated inconsistent WAN performance and rising receive-error counters on my firewall WAN interface. Rather than assume that the ISP connection or firewall hardware was at fault, I isolated the issue by testing a separate client directly against the fiber handoff.

The direct test achieved near-gigabit throughput with stable gateway, DNS, and Internet connectivity. After moving the firewall WAN assignment to an alternate physical interface, reconnecting the WAN path, and retesting, normal near-gigabit download performance returned. I retained the alternate WAN interface assignment as the production configuration.

## Symptoms

I observed inconsistent download throughput through the firewall, along with a growing inbound error counter on the WAN interface. Link negotiation remained at full duplex, so the issue was not immediately explained by an obvious speed or duplex mismatch.

## Investigation

I used a layered process to isolate the affected part of the connection:

1. I reviewed the firewall WAN interface state, negotiated speed and duplex, addressing, gateway status, and error counters.
2. I moved the logical WAN assignment to a different physical interface to determine whether the issue was isolated to one port.
3. Receive errors still appeared after the move, so I did not treat the original interface as conclusively defective.
4. I disconnected the firewall from the fiber handoff and connected a Mac directly to the handoff through Ethernet.
5. I disabled Wi-Fi to ensure that the test used only the direct wired WAN connection.
6. I confirmed a public DHCP lease, a default route, stable gateway reachability, external IP connectivity, and DNS resolution.
7. I measured throughput with multiple tests, using a browser-based speed test as the primary comparison because command-line server selection produced inconsistent results.

## Results

The direct connection achieved near-gigabit download and upload performance with stable latency. This showed that the ISP service, fiber handoff, Ethernet cable path, and test client could deliver expected service levels.

After reconnecting the firewall on the alternate physical WAN interface, near-gigabit download performance returned through the firewall. The alternate interface was retained as the production WAN assignment.

## Outcome

The immediate WAN performance issue cleared after the controlled disconnect, interface reassignment, and reconnection sequence. I retained the alternate WAN interface assignment and left speed and duplex configured for auto-negotiation.

Because the final validation showed normal performance through the firewall, I did not replace firewall hardware or escalate the incident as an ISP line fault. I retained ongoing monitoring as the appropriate next step because the original issue appeared intermittent.

## Lessons Learned

- A negotiated 1 Gbps link does not prove error-free traffic flow.
- Interface error counters provide useful evidence during WAN troubleshooting.
- A direct-bypass test can distinguish ISP/fiber-handoff issues from firewall, NIC, cable, or local processing issues.
- One benchmark result is not sufficient; test methodology and server selection can affect reported results.
- A controlled reconnect can clear a transient link-state problem, but ongoing monitoring is necessary before claiming a permanent root cause.
- I should use the minimum necessary change and validate it before replacing hardware or escalating to an upstream provider.

## Follow-Up Validation

I would treat the issue as recurring if WAN performance drops again or receive errors increase rapidly during sustained traffic. If that occurs, I will compare error growth under load, update the firewall platform software and NIC firmware where available, temporarily disable optional inspection or shaping features for controlled testing, and test with a different supported Ethernet controller.