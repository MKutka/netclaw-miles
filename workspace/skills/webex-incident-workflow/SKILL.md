---
name: webex-incident-workflow
description: "Manage network incident response workflows in Webex - incident spaces, status updates, escalation, resolution tracking, and post-incident review coordination"
user-invocable: true
metadata:
  { "openclaw": { "requires": { "bins": ["python3"] } } }
---

# Webex Incident Workflow

Coordinate network incident response through Webex using NetClaw's agent capabilities. This skill defines structured workflows for incident detection, triage, investigation, resolution, and post-incident review — all conducted through Webex spaces and reply threads.

## Webex Capabilities Used

| Capability | Purpose |
|------------|---------|
| Send to space | Post incident updates to NetClaw Incidents (or designated space) |
| Threaded replies | All investigation and status updates in the same thread |
| File attachments | Attach logs, configs, diagrams |
| Mentions | @mention on-call or IC when escalating |

## Incident Lifecycle in Webex

### Phase 1: Detection & Declaration

When a critical alert triggers (from webex-network-alerts skill or human report):

```
🚨 **INCIDENT DECLARED — Network Outage**
**Severity:** P1 — Service Impacting
**Detected:** 2024-02-21 14:32 UTC
**Reporter:** NetClaw (automated) / @engineer1 (manual)

**Symptoms:**
• R1 unreachable (ping 0%)
• 47 downstream routes lost
• 3 OSPF adjacencies down
• BGP peer to ISP: IDLE

**Impact:**
• Site A has no WAN connectivity
• Estimated affected users: ~200

**Incident Commander:** [awaiting claim — reply "I'll take IC" to claim]
**ServiceNow:** [CR/INC pending]

━━━ **All investigation updates in this thread** ━━━
```

### Phase 2: Triage & Assignment

When an engineer replies claiming IC:

```
👥 **Incident Team Formed**
**IC:** @engineer1 (claimed at 14:35 UTC)
**NetClaw:** Automated investigation assistant

**Triage Checklist:**
✅ Alert generated and posted
✅ Incident declared (P1)
✅ IC assigned → @engineer1
⬜ ServiceNow incident created
⬜ Upstream device checked
⬜ Blast radius confirmed
⬜ Customer communication sent

_NetClaw beginning automated investigation..._
```

### Phase 3: Automated Investigation

NetClaw runs diagnostics and posts results in the reply thread:

```
🔍 **Automated Investigation — Step 1/4**
_Checking upstream device R2 for connectivity to R1..._
```

Post each step result as a reply:

```
🔍 **Investigation Results — Step 1/4**
**Ping from R2 to R1 (10.1.1.1):** 0% success — R1 unreachable from upstream

🔍 **Investigation Results — Step 2/4**
**R2 interface Gi1 (toward R1):** up/up, 0 CRC errors, last input 4 min ago
→ Physical layer looks OK from R2 side

🔍 **Investigation Results — Step 3/4**
**R2 OSPF neighbors:** R1 missing from neighbor table (was FULL)
→ OSPF adjacency lost, DR election may be in progress

🔍 **Investigation Results — Step 4/4**
**R2 logs (last 30 min):**
14:31:47: %OSPF-5-ADJCHG: Nbr 1.1.1.1 on Gi1 from FULL to DOWN
14:31:48: %LINEPROTO-5-UPDOWN: Line protocol on Gi1, changed to down
14:32:01: %LINEPROTO-5-UPDOWN: Line protocol on Gi1, changed to up
14:32:15: %OSPF-5-ADJCHG: Nbr 1.1.1.1 on Gi1 from DOWN to INIT

**Analysis:** R2 saw Gi1 flap at 14:31. Line protocol came back up but OSPF hasn't re-converged. Likely physical issue on R1 side causing interface bounce.
```

### Phase 4: Status Updates

Post periodic status updates as replies:

```
⏳ **Status Update — 14:50 UTC (18 min elapsed)**
**Status:** Investigating
**Finding:** R1 appears to have reloaded unexpectedly. R2 sees the link recover but R1 is not responding to OSPF hellos yet. Possible crash or power event.
**Next Step:** Waiting for R1 to complete boot sequence. Checking console access.
**ETA:** Unknown — dependent on R1 recovery

_ServiceNow INC0012345 updated_
```

### Phase 5: Resolution

```
✅ **INCIDENT RESOLVED**
**Duration:** 34 minutes (14:32 — 15:06 UTC)
**Resolution:** R1 experienced a software crash (Traceback in logs). Device auto-reloaded and recovered. All OSPF adjacencies re-established. Full routing restored.

**Post-Resolution Verification:**
• R1 reachable: ✅ 100% ping success
• OSPF neighbors: ✅ 3/3 FULL
• BGP peer: ✅ Established
• Route count: ✅ 47 routes (matches baseline)
• Connectivity: ✅ 100% to all targets

**Root Cause:** Software crash — Traceback found in logs indicating bug CSCxx12345. TAC case recommended.

**ServiceNow:** INC0012345 resolved
**GAIT:** Session abc123 closed
```

### Phase 6: Post-Incident Review

```
📋 **Post-Incident Review — Scheduled**
**Incident:** Network Outage — R1 crash
**Date:** 2024-02-22 10:00 UTC
**Space:** This thread

**Review Artifacts (attached):**
1. 📄 Timeline of events
2. 📄 R1 show logging output
3. 📄 R1 show version (confirms reload reason)
4. 📄 GAIT audit trail (full session)
5. 📄 Pre/post health check comparison

**Discussion Topics:**
• Was detection fast enough?
• Was automated investigation helpful?
• What monitoring gaps exist?
• Should R1 be upgraded to patched version?
• Do we need redundant path for this link?
```

## Escalation Matrix

```
⬆️ **Escalation Guide**

| Severity | Notify | Escalate After | Space |
|----------|--------|----------------|-------|
| P1 | IC + Manager + NOC | 15 min | NetClaw Incidents |
| P2 | IC + Team | 30 min | NetClaw Alerts |
| P3 | Assigned engineer | 4 hours | NetClaw Alerts |
| P4 | Queue only | Next business | NetClaw General |
```

When escalating in Webex, @mention the appropriate person. Respect allowlist and availability (see webex-user-context skill where applicable).

## Reply-Based Status Tracking

Use reply messages to indicate status:

| Reply convention | Status | Meaning |
|------------------|--------|---------|
| "IC claimed" | Claimed | IC has taken ownership |
| "Investigating" | Investigating | Active investigation |
| "Fix being applied" | Fixing | Fix in progress |
| "Waiting on vendor/ISP" | Waiting | Waiting on external |
| "Resolved" | Resolved | Incident resolved |
| "PIR scheduled" | PIR Scheduled | Post-incident review planned |

## ServiceNow Integration

Create ServiceNow incident at Phase 1:

```bash
python3 $MCP_CALL "python3 -u $SERVICENOW_MCP_SCRIPT" create_incident \
  '{"short_description":"P1 - R1 unreachable, WAN outage Site A","description":"R1 is unreachable. 47 routes lost, 3 OSPF adjacencies down. Impact: ~200 users at Site A without WAN connectivity.","urgency":"1","impact":"1","category":"Network"}'
```

Update ServiceNow as incident progresses and close on resolution.

## GAIT Audit Trail

Record every phase in GAIT:

```bash
python3 $MCP_CALL "python3 -u $GAIT_MCP_SCRIPT" gait_record_turn \
  '{"input":{"role":"assistant","content":"INCIDENT P1: R1 unreachable. Phase 1 declared, Phase 2 IC assigned @engineer1, Phase 3 automated investigation shows R1 crash, Phase 4 monitoring recovery, Phase 5 resolved after 34 min. INC0012345 closed.","artifacts":[]}}'
```
