# Monitoring and Operations

## Objective

I use monitoring and operational checks to detect service failures, validate infrastructure changes, and shorten troubleshooting time. The goal is to know whether a failure is caused by the network, a virtualization host, an application, or an external dependency before starting corrective work.

## Monitoring approach

| Monitoring area | Example checks | Operational value |
|---|---|---|
| Service availability | HTTP/S, TCP, ICMP, and application health checks | Detects loss of access or application failure |
| Infrastructure reachability | Gateway, switch, AP, hypervisor, and service-network checks | Helps isolate network-path and platform failures |
| Internet performance | Scheduled speed tests and historical results | Identifies provider degradation and trends |
| Notifications | Service-state alerts | Reduces the time between an outage and awareness |
| Traffic visibility | Local flow monitoring and firewall logs | Supports troubleshooting and policy validation |

## Operational practices

- I keep monitoring and utility workloads separate from primary application workloads when practical.
- I validate monitoring from more than one network perspective after changing VLANs, firewall policy, or service placement.
- I use documented runbooks and incident notes to record symptoms, tests, root causes, remediation, and prevention actions.
- I review denied firewall traffic and flow data when validating a new service dependency or segmentation rule.
- I consider persistent data, startup order, and backup coverage before making service changes.

## Design lesson

Monitoring is most useful when it reflects the user-visible service path rather than only confirming that a host is powered on. A responsive hypervisor does not prove an application is reachable, and an application failure does not necessarily indicate a network failure. Layered checks make those distinctions easier to diagnose.
