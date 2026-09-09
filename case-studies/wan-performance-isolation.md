# Investigating WAN Performance Degradation and NIC Stability

## Summary

I investigated inconsistent WAN performance and rising receive-error counters on the firewall WAN interface. I used a direct-handoff test to separate the upstream ISP and fiber path from the local firewall path, then tracked the issue through configuration changes and a later firewall-platform update.

The direct test demonstrated that the upstream connection could deliver near-gigabit performance. Moving the WAN assignment to an alternate physical interface was a useful troubleshooting step and remains the production connection, but later evidence indicated that the OPNsense platform update and its Intel network-driver stability improvements were the likely resolution.

## Symptoms

I observed inconsistent download throughput through the firewall, along with a growing inbound error counter on the WAN interface. The Ethernet link negotiated at full duplex, so the issue was not explained by an obvious speed or duplex mismatch.

## Investigation

I used a layered process to isolate the affected part of the connection:

1. I reviewed the firewall WAN interface state, negotiated speed and duplex, addressing, gateway status, and error counters.
2. I moved the logical WAN assignment to a different physical interface to determine whether the issue was isolated to one port.
3. Receive errors were still observed after the interface move, so I did not treat the original interface as conclusively defective.
4. I disconnected the firewall from the fiber handoff and connected a Mac directly to the handoff through Ethernet.
5. I disabled Wi-Fi to ensure testing used only the direct wired WAN connection.
6. I confirmed a public DHCP lease, default route, stable gateway reachability, external IP connectivity, and DNS resolution.
7. I measured throughput with multiple tests, using a browser-based speed test as the primary comparison because command-line server selection produced inconsistent results.

## Results

The direct connection achieved near-gigabit download and upload performance with stable latency. This showed that the ISP service, fiber handoff, cable path, and test client could deliver expected service levels.

The WAN assignment remained on the alternate physical interface after the troubleshooting session. However, because receive errors were not conclusively eliminated by the port move alone, I continued monitoring the firewall NIC behavior instead of declaring the hardware path fixed.

## Resolution

A later OPNsense update included an Intel network-driver stability improvement addressing receive-side issues associated with the firewall platform's integrated NICs. After applying the update, the production WAN interface remained stable under high gigabit load without increasing input-error counters.

I retained the alternate WAN interface assignment because it was already functioning as the production configuration, but I record the platform update—not the port change—as the likely resolution.

## Lessons Learned

- A negotiated 1 Gbps link does not prove error-free traffic flow.
- Interface error counters are useful evidence during WAN troubleshooting.
- A direct-bypass test can distinguish ISP/fiber-handoff issues from firewall, NIC, cable, or local processing issues.
- A configuration change can coincide with recovery without being the actual root-cause fix.
- I should validate stability over time before declaring an intermittent hardware, driver, or physical-layer issue resolved.
- Documentation should be corrected when later evidence changes the root-cause conclusion.

## Follow-Up Validation

I continue to monitor WAN throughput and input-error counters after firewall updates and sustained network use. If the issue recurs, I will compare error growth under load, review platform and NIC firmware availability, temporarily disable optional inspection or shaping features for controlled testing, and test with a different supported Ethernet controller.