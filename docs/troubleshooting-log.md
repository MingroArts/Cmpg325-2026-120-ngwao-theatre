# Troubleshooting Log — CMPG325-2026-120

This log documents two configuration issues encountered during implementation, how each was diagnosed, and how each was resolved and re-verified. Both are included here in full rather than cleaned up out of the history, since the testing and recovery process is itself part of the project's evidence.

---

## Incident 1 — Switch Configuration Loss (VLANs, Trunking, EtherChannel)

**When found:** While testing basic connectivity after assigning static IPs to end devices.

**Symptom:** A ping from Finance PC to its own gateway (10.47.0.161) failed with 100% loss, despite the router showing all VLAN subinterfaces as up/up.

**Diagnosis steps:**
1. Confirmed the PC's own IP configuration with `ipconfig` — correct (10.47.0.162/27, gateway 10.47.0.161).
2. Confirmed Router1's side with `show ip interface brief` — all 7 subinterfaces up/up with correct addresses. Router side ruled out.
3. Moved to Switch-Access and ran `show interfaces status` — found every port sitting in VLAN 1 (default), including the port connecting to Finance PC. The switch hostname had also reverted to `Switch`, confirming this was not the configured device state.
4. Checked `show vlan brief` on both Switch-Access and Switch-Core — neither switch retained any of the previously configured VLANs (10, 20, 30, 40, 50, 60, 99). Both had reset to factory default.

**Root cause:** The switch running-configurations were lost, most likely because the Packet Tracer session was closed before a `write memory` was issued after the most recent round of changes, and an older (unsaved) state was reloaded on reopening the file.

**Fix applied:** Both switches were fully reconfigured from scratch — VLAN database recreated on each, trunk/EtherChannel ports re-bundled (`channel-group 1 mode active` on Fa0/1 + Fa0/4 on Switch-Core, Fa0/1 + Fa0/6 on Switch-Access), and department ports reassigned to their correct VLANs on Switch-Access.

**Verification:**
- `show etherchannel summary` on both switches returned `Po1(SU)` with both member ports flagged `(P)` — bundle restored.
- `show vlan brief` confirmed all department ports back in their correct VLANs.
- Finance → own gateway ping retested: 4/4 success.

**Lesson applied going forward:** `write memory` issued after every discrete change rather than only at the end of a block, and the `.pkt` file saved (Ctrl+S) at the end of every session.

---

## Incident 2 — Router ACLs Missing After DHCP Configuration

**When found:** While re-testing Finance isolation after switching department PCs from static IPs to DHCP.

**Symptom:** Finance PC successfully pinged Admin's newly-assigned DHCP address (10.47.0.136) — four replies with TTL=127, indicating the traffic was being routed through Router1 rather than blocked, when it should have been denied by the Finance isolation ACL.

**Diagnosis steps:**
1. Confirmed Finance's own DHCP lease was correct and within the expected VLAN 10 range.
2. Ran `show access-lists` on Router1 — returned nothing. No ACLs were present at all.
3. Ran `show ip interface GigabitEthernet0/0.10` — confirmed "Inbound access list is not set" and "Outgoing access list is not set", meaning the Finance isolation ACLs (110, 120) were both missing and unbound from the interface.

**Root cause:** The same underlying issue as Incident 1 — the router's access-list configuration had not persisted across a save/reopen cycle, despite `write memory` having been run after the ACLs were first configured in an earlier session.

**Fix applied:** All three ACLs were recreated on Router1 (110 and 120 for Finance isolation, 130 for the CR4 server restriction) and re-applied to their respective subinterfaces (Gi0/0.10 and Gi0/0.50). `show access-lists` was used immediately afterward to confirm all three were present and correctly bound before proceeding.

**Verification:**
- Finance → Admin's new DHCP address: now correctly blocked ("Destination host unreachable").
- Finance → own gateway: still successful.
- Admin, Box Office, and Production → file server: still successful.
- Finance and Guest WiFi → file server: still correctly blocked.
- A full close-and-reopen test was then performed on the `.pkt` file specifically to confirm this would not recur: `show access-lists`, `show ip dhcp binding`, and `show etherchannel summary` were all re-checked from a cold start and found intact.

**Lesson applied going forward:** Configuration exports (`show running-config`) for all three devices are now kept in `/Configs` in the GitHub repository as a recovery reference, in addition to the `write memory` discipline adopted after Incident 1.

---

## Summary

Both incidents stemmed from the same root cause (configuration not persisting across a Packet Tracer session reload) rather than any error in the configuration logic itself — in both cases, the original commands were correct and worked as intended once re-applied. Catching both issues relied on the project's own testing routine (re-running connectivity tests after each change) rather than on it being visibly broken, which is why a full end-to-end re-verification and a dedicated save-and-reopen persistence check were carried out before considering implementation complete.
