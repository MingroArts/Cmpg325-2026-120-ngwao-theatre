# CMPG325-2026-120 — Ngwao Cultural Theatre Network Project

Individual semester project for CMPG 325 (Computer Networks), NWU Department of Computer Science and Information Systems.

## Project Details
| | |
|---|---|
| Student | Sangweni, MC |
| Student Number | 54157722 |
| Project ID | CMPG325-2026-120 |
| Client ID | CLI-120 |
| Assigned Organisation | Ngwao Cultural Theatre (Mahikeng) |
| Industry | Entertainment |
| Addressing Block | 10.47.0.0/16 |

## Client Requirements
- Provide appropriate connectivity and network services for the assigned scenario
- Design constraint: **Finance department must be isolated from all general users**
- Change request CR4: a new application/file server must be installed and reachable only by authorised departments
- Produce a working, testable Packet Tracer implementation

## Assigned Networking Challenge
**EtherChannel (link aggregation)** — Advanced difficulty. Configured and verified between Switch-Core and Switch-Access using two bundled Fast Ethernet links running LACP (`Po1(SU)`, both member ports confirmed `(P)` on both switches).

## What's Implemented

### Network Design
- 7 VLANs routed via Router1 (router-on-a-stick, one 802.1Q subinterface per VLAN)
- VLSM addressing plan across 10.47.0.0/16, sized to each department's host requirement
- Two-tier switching: Switch-Core (server, AP, router trunk) and Switch-Access (department PCs), linked by an EtherChannel bundle

### Security & Access Control
- **Finance isolation** (ACLs 110/120 on Gi0/0.10): Finance can reach its own gateway/VLAN but is blocked from every other VLAN, in both directions. Verified working.
- **CR4 file server restriction** (ACL 130 on Gi0/0.50): only Admin, Box Office, and Production can reach the new file/app server; Finance and Guest WiFi are blocked. Verified via ping and Simple PDU testing.

### Network Services
- **DHCP** configured for all 7 VLANs, with static exclusions protecting the router gateways and manually-assigned devices. All 4 department PCs confirmed pulling correct leases (`show ip dhcp binding`).

### Testing
- Full end-to-end connectivity sweep completed: inter-VLAN routing, Finance isolation, CR4 restriction, DHCP leasing, and Guest WiFi wireless connectivity all independently verified.
- Configuration persistence confirmed via a save-and-reopen test on the .pkt file (all ACLs, DHCP bindings, and EtherChannel state survived a full close/reopen).

## Repository Structure
```
/design           — client requirements, physical & logical topology diagrams, IP addressing plan
/packet-tracer     — final working .pkt file
/Screenshots        — EtherChannel verification, ACL testing (before/after), DHCP bindings, end-to-end connectivity evidence
/Configs           — full running-config exports for Router1, Switch-Core, and Switch-Access
```

## Project Milestones
- **Commencement:** 14 August 2026
- **Milestone 1 (Client Design Review):** 28 August 2026 ✅ — client requirements, physical topology, logical topology, IP addressing plan, initial GitHub repo
- **Milestone 2 (Client Implementation Review):** 2 October 2026 — working .pkt file, feature implemented, testing evidence, updated GitHub portfolio
- **Final Submission:** 16 October 2026 — .pkt file, GitHub portfolio, technical report, 15-20 min video

## Status
🟢 Implementation complete — full connectivity, security, and services verified. Preparing Milestone 2 submission and final documentation/video.

---
*This repository is my own individual work in accordance with the CMPG325 project brief and NWU AI Policy.*
