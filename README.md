# Multi-Protocol Routing: OSPF, RIPv2, and Static Route Redistribution

A simulated enterprise network built in **GNS3**, demonstrating multi-area OSPF, RIPv2, static routing, and route redistribution between all three — with floating static backup links for redundancy.

## Overview

This project models a 6-router, 4-LAN topology where different parts of the network run different routing protocols, and must be redistributed into a single cohesive routing domain:

- **OSPF** across 3 areas (Area 0, Area 1, Area 2)
- **RIPv2** on the edge (R3 and R5)
- **Static routing** at the network edge (R6) and for backup paths
- **Two floating static backup links** that activate only on primary link failure



| Router | Protocol(s) | Role |
|--------|-------------|------|
| R1 | OSPF (Area 0/1) | ABR + static backup to R4 |
| R2 | OSPF (Area 0/2) | ABR |
| R3 | OSPF (Area 1) + RIPv2 | RIP↔OSPF redistribution point |
| R4 | OSPF (Area 1/2) + Static | ABR + Static↔OSPF redistribution + 2 floating backups |
| R5 | RIPv2 | Floating static backup link to R4 |
| R6 | Static only | Default route to R4 |

## Network Design

**Router-to-router links (primary):**

| Link | Router A | IP (A) | Router B | IP (B) | Network |
|------|----------|--------|----------|--------|---------|
| 1 | R1 | 10.1.12.1 | R2 | 10.1.12.2 | 10.1.12.0/30 |
| 2 | R1 | 10.1.13.1 | R3 | 10.1.13.2 | 10.1.13.0/30 |
| 3 | R2 | 10.1.24.1 | R4 | 10.1.24.2 | 10.1.24.0/30 |
| 4 | R3 | 10.1.34.1 | R4 | 10.1.34.2 | 10.1.34.0/30 |
| 5 | R3 | 10.1.35.1 | R5 | 10.1.35.2 | 10.1.35.0/30 |
| 6 | R4 | 10.1.46.1 | R6 | 10.1.46.2 | 10.1.46.0/30 |

**Backup links (floating static, AD 150):**

| Link | Router A | Router B | Network |
|------|----------|----------|---------|
| Backup 1 | R1 | R4 | 10.1.14.0/30 |
| Backup 2 | R5 | R4 | 10.1.36.0/30 |

**LANs:**

| LAN | Router | Gateway IP | Hosts |
|-----|--------|------------|-------|
| LAN 1 | R1 | 192.168.1.1 | PC1, PC2 |
| LAN 2 | R2 | 192.168.2.1 | PC3, PC4 |
| LAN 3 | R5 | 192.168.5.1 | PC5 |
| LAN 4 | R6 | 192.168.6.1 | PC6 |

## Deliverables
- **GNS3** folder containing router configuration files, GNS3 project files.
- **Sceen shots** folder containing topology design, ping tests and other important verification tests.
- A **PDF** file containing the full documentation.

## Key Configuration Concepts

- **Multi-area OSPF:** R1, R2, and R4 act as ABRs, connecting Area 0 (backbone) to Areas 1 and 2.
- **Redistribution:** R3 redistributes between OSPF and RIPv2; R4 redistributes between OSPF and static routes.
- **Floating static routes:** Configured with administrative distance 150 so they only activate when the primary OSPF/RIP path fails.
- **Verification:** Routing tables, OSPF neighbor adjacencies, and end-to-end reachability were all verified using `show ip route`, `show ip ospf neighbor`, `ping`, and `traceroute` from simulated PCs (VPCS).

## Testing & Results

- All 4 LANs successfully reached each other end-to-end.
- Ping and traceroute results confirmed expected hop counts and latency proportional to topological distance (e.g., PC1 → PC6 took 5 hops; PC1 → PC3 took 2 hops).
- Backup links were verified to remain inactive during normal operation and to engage only when a primary link was simulated as down.

## Tools Used

- **GNS3** — network simulation
- **Cisco IOS** (routers) / **VPCS** (virtual PCs)
- Standard IOS CLI for OSPF, RIP, and static route configuration

## Author
Babar Khan - Networking intern at ITSimplera 
