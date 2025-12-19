# COE 344 Network Project — OSPF Primary/Backup + DHCP/DNS/HTTP Validation

## Overview
This lab implements a 6-router OSPFv2 (Area 0) topology with multiple LANs and services to demonstrate end-to-end reachability, deterministic routing (primary/backup), and failover behavior.

Key outcomes:
- OSPFv2 adjacencies formed across all backbone links (Area 0)
- Primary path enforced using OSPF interface cost tuning
- Backup path validated using a controlled failure test (shutdown of a primary link)
- Services deployed for validation: DHCP, DNS, and HTTP servers

---

## Topology
- 6 routers (RTR1–RTR6)
- Multiple LAN segments (end hosts + servers)
- Point-to-point inter-router links (commonly /30)

Suggested files:
- Topology diagram: `images/topology.png`
- Packet Tracer file: `pkt/COE344_Project.pkt` (rename to match your file)

---

## Routing Design (OSPFv2)
- Routing protocol: OSPFv2, Area 0
- Primary vs backup forwarding implemented by tuning OSPF interface costs:
  - Primary links = lower cost
  - Backup links = higher cost

What this achieves:
- Under normal operation, traffic follows the preferred (lowest-cost) route
- During failure of the primary link, OSPF reconverges and traffic shifts to the backup route

---

## Services Implemented (End-to-End Validation)
### DHCP
- DHCP service configured (per project requirements) to assign IP configuration to LAN hosts.

### DNS
- DNS configured with A-records for:
  - `www1.coe344.edu`
  - `www2.coe344.edu`
  - `www3.coe344.edu`

### HTTP Servers
- Three HTTP servers placed in separate subnets and reachable across the routed network.

---

## Verification
I validated functionality using standard networking checks:

OSPF:
- `show ip ospf neighbor`
- `show ip route`

Connectivity:
- `ping` between hosts/routers and to servers
- `traceroute` to confirm path selection

Services:
- DHCP address assignment to hosts
- DNS name resolution for `www1/2/3.coe344.edu`
- HTTP access to each web server

---

## Primary/Backup Path Validation (Traceroute)
### Normal State (Primary Path)
- Traceroute confirms the preferred hop sequence via the primary route.

### Failure Test (Primary Link Shutdown)
I simulated a failure by shutting down the interface on a primary inter-router link.

Failure action (example):
- `conf t`
- `interface <PRIMARY_INTERFACE>`
- `shutdown`

Observed behavior:
- OSPF adjacency drops on the failed link
- OSPF recalculates best paths
- Traffic shifts to the backup path
- End-to-end reachability remains intact (ping + DNS + HTTP)

Post-failure checks:
- `show ip ospf neighbor`
- `show ip route`
- `traceroute` (hop sequence changes)
- DNS + HTTP access still successful

---

## How to Run
1. Open the Packet Tracer file: `pkt/COE344_Project.pkt`
2. Review the topology and device configurations (routers + servers + hosts)
3. Run baseline checks:
   - OSPF neighbors and routes
   - Ping/traceroute to servers
   - DNS + HTTP tests
4. Run the controlled failure test:
   - Shutdown the primary link interface
   - Re-run checks and confirm backup path + service continuity
5. Restore link:
   - `no shutdown`
   - Confirm return to primary path

---
