# IP Addressing Plan — Ngwao Cultural Theatre

Assigned block: **10.47.0.0/16**

Subnetted using VLSM, sized to each department's actual host requirement rather than flat equal-sized blocks, to use the address space efficiently.

| VLAN | Department | Hosts Needed | Subnet | Usable Range | Gateway (Router1 subinterface) |
|---|---|---|---|---|---|
| 60 | Guest WiFi | 50 | 10.47.0.0/26 | 10.47.0.1 – 10.47.0.62 | 10.47.0.1 |
| 40 | Production | 30 | 10.47.0.64/26 | 10.47.0.65 – 10.47.0.126 | 10.47.0.65 |
| 20 | Admin | 20 | 10.47.0.128/27 | 10.47.0.129 – 10.47.0.158 | 10.47.0.129 |
| 10 | Finance | 20 | 10.47.0.160/27 | 10.47.0.161 – 10.47.0.190 | 10.47.0.161 |
| 99 | Management (native) | 10 | 10.47.0.192/28 | 10.47.0.193 – 10.47.0.206 | 10.47.0.193 |
| 30 | Box Office | 10 | 10.47.0.208/28 | 10.47.0.209 – 10.47.0.222 | 10.47.0.209 |
| 50 | Servers | 5 | 10.47.0.224/29 | 10.47.0.225 – 10.47.0.230 | 10.47.0.225 |

## Design Notes
- Router1 uses router-on-a-stick: one physical interface (Gi0/0) split into 7 subinterfaces (Gi0/0.10 – Gi0/0.99), each tagged with 802.1Q encapsulation matching its VLAN number.
- VLAN 99 is configured as the native (untagged) VLAN for switch/router management traffic.
- Finance (VLAN 10) is isolated from all other VLANs via ACL on Router1, satisfying the design constraint.
- The Servers VLAN (50) hosts the new application/file server required by change request CR4, restricted to authorised departments only via ACL.
