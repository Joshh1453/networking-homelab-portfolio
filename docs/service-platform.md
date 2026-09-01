# Service Platform

## Overview

I use the homelab to host and operate a mix of home-automation, media, game, monitoring, notification, network-management, and administrative workloads. The environment is designed as a learning platform for virtualization, Linux administration, service lifecycle management, network segmentation, and recovery planning.

## Workload categories

| Category | Examples | Operational focus |
|---|---|---|
| Home automation | Home-automation and device-integration services | Network access, device dependencies, and persistent configuration |
| Media and gaming | Media streaming and game-server workloads | Resource planning, storage, controlled client access, and recovery |
| Monitoring and notifications | Availability monitoring, performance tracking, and notifications | Independent observability and timely outage awareness |
| Network management | Wireless-controller and traffic-visibility services | VLAN-aware access, secure administration, and configuration validation |
| Administrative tooling | Remote-management and container-management platforms | Authentication, access control, backups, and service ownership |

## Service lifecycle

I document each workload's purpose, platform, startup behavior, resource needs, persistent-data considerations, backup coverage, and recovery dependencies. This creates a repeatable process for deploying a workload, troubleshooting it, moving it, or restoring it after a failure.

## Placement and access

Services are attached to the network zone appropriate for their role. Client-facing applications are made reachable only from approved trusted networks, while management interfaces remain limited to authorized administration paths. Remote administration uses the VPN design documented in this repository rather than direct internet exposure.

## Operational lesson

Self-hosting is more than making applications run. Reliable operation requires managing startup behavior, persistent data, update risk, dependencies, monitoring, access policy, backups, and rollback options. I use the lab to practice that full lifecycle.