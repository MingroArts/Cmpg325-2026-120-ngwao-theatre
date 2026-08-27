# Client Requirements — Ngwao Cultural Theatre (CLI-120)

## Client Background
Ngwao Cultural Theatre is an entertainment-industry client requiring a network that supports its administrative, financial, box office, and production operations, along with guest connectivity for patrons.

## Requirements Analysis

**Departments / user groups identified:**
- Finance
- Admin
- Box Office
- Production
- Servers (application/file services)
- Guest WiFi (patron access)
- Management (network device administration)

**Core requirements:**
- Provide appropriate connectivity and network services across all departments using the assigned addressing block 10.47.0.0/16
- Segment the network logically by department using VLANs, routed via a single Router1 (router-on-a-stick design)
- Ensure the network is testable and demonstrable end-to-end in Cisco Packet Tracer

**Design constraint:**
- The Finance department must be fully isolated from all general users. This is addressed by placing Finance in its own VLAN (10) and applying an ACL on the router to block inter-VLAN traffic to/from Finance.

**Change request CR4:**
- A new application/file server must be installed and reachable only by authorised departments. This is addressed by placing the server in a dedicated Servers VLAN (50) with ACL restrictions limiting access to authorised department VLANs only.

**Assigned networking challenge:**
- EtherChannel (link aggregation), Advanced difficulty. Implemented between Switch-Core and Switch-Access using two bundled physical links running LACP, providing increased bandwidth and redundancy between the core and access layers.

## Scope
This solution addresses only the Ngwao Cultural Theatre scenario as assigned (Project ID CMPG325-2026-120, Client ID CLI-120) and has not been substituted with any other client scenario.
