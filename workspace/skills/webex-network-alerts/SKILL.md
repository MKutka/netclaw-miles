---
name: webex-network-alerts
description: "Format and deliver network alerts, health warnings, and critical notifications via Webex with markdown formatting, replies, and file attachments"
user-invocable: true
metadata:
  { "openclaw": { "requires": { "bins": ["python3"] } } }
---

# Webex Network Alerts

NetClaw can operate via the Webex channel (OpenClaw plugin [@jimiford/webex](https://github.com/JimiHFord/openclaw-webex)). This skill defines how to format and deliver network alerts through Webex spaces, using markdown and threaded replies.

## Webex Capabilities Used

| Capability | Purpose |
|------------|---------|
| Send to space | Post alert messages to Webex spaces (rooms) |
| Markdown | **Bold**, _italic_, and structured text in messages |
| Threaded replies | Follow-up investigation and diagnostics under the original message |
| File attachments | Attach diagrams, reports, and log excerpts via public URLs |
| Mentions | @mention users in spaces when escalating |

## Alert Severity Formatting

Use markdown and emoji to visually distinguish severity levels in Webex.

### CRITICAL Alert Format

```
🚨 **CRITICAL — Device Unreachable**

**Device:** R1 (10.1.1.1)
**Detected:** 2024-02-21 14:32 UTC
**Impact:** 47 downstream routes affected, 3 OSPF adjacencies lost

**Symptoms:**
• Ping 0% success (was 100%)
• OSPF neighbor state: DOWN
• BGP peer 10.1.1.2: IDLE

**Recommended Action:**
1. Check physical connectivity / power
2. Verify interface status on upstream device
3. Check for reload reason if device recovers

_Reply thread for investigation updates →_
```

### HIGH Alert Format

```
⚠️ **HIGH — Interface Flapping**

**Device:** SW1 | **Interface:** Gi1/0/24
**Flap Count:** 12 in last 30 minutes
**Connected To:** R2:Gi0/0/1 (discovered via CDP)

**Action Required:** Investigate physical layer — CRC errors detected (47 in 5 min)
```

### WARNING Alert Format

```
🟡 **WARNING — Memory Pressure**

**Device:** R2 | **Memory:** 82% used (2.1G / 2.6G)
**Trend:** Increased from 71% over 24h
**Top Consumer:** BGP Router (843 MB)

_Monitor — no immediate action required_
```

### INFORMATIONAL Alert Format

```
ℹ️ **INFO — Configuration Change Detected**

**Device:** R1 | **User:** admin (10.0.0.50)
**Time:** 2024-02-21 09:15 UTC
**Change:** 3 lines modified in running-config

_Captured in GAIT session abc123_
```

## Alert Workflows

### Health Check Alert Flow

After running a health check (pyats-health-check skill), post results to the designated space:

1. Run the full health check procedure
2. Determine overall severity (CRITICAL > HIGH > WARNING > HEALTHY)
3. Format the alert using the appropriate severity template above
4. Post to the NetClaw Alerts (or NetClaw Reports) space
5. If CRITICAL or HIGH, use 🚨 or ⚠️ in the message
6. If CRITICAL, post a reply in the same thread with detailed diagnostics

### Threshold-Triggered Alerts

When any health check metric exceeds thresholds:

| Metric | WARNING | HIGH | CRITICAL |
|--------|---------|------|----------|
| CPU 5min avg | > 50% | > 75% | > 90% |
| Memory used | > 70% | > 85% | > 95% |
| Interface errors | > 0 CRC | > 100/min | Resets incrementing |
| Packet loss | > 0% | > 5% | > 20% |
| NTP offset | > 100ms | > 500ms | > 1s or unsync |
| BGP peer | Flapping | 1 peer down | Multiple peers down |
| OSPF adjacency | Flapping | 1 adj lost | Area partition |

### Multi-Device Fleet Alert Summary

After a fleet-wide health check, post a single summary:

```
📊 **Fleet Health Summary — 2024-02-21 14:00 UTC**

**Devices Checked:** 8 | 🔴 1 CRITICAL | ⚠️ 2 HIGH | 🟡 3 WARNING | ✅ 2 HEALTHY

| Device | CPU | Mem | Intf | NTP | Overall |
|--------|-----|-----|------|-----|---------|
| R1     | ✅ 12% | ⚠️ 78% | ✅ | ✅ | ⚠️ WARNING |
| R2     | ✅ 8%  | ✅ 45% | 🔴 Gi2 | ✅ | 🔴 CRITICAL |
| SW1    | ⚠️ 67% | ✅ 52% | ✅ | 🔴 unsync | 🔴 HIGH |

_Details in reply thread →_
```

### Security Alert Flow

After a security audit (pyats-security skill), post findings:

```
🛡️ **Security Audit — R1**

**Findings:** 2 CRITICAL | 2 HIGH | 3 MEDIUM | 1 LOW

🔴 **CRITICAL:**
• [C-001] SSHv1 enabled — MITM vulnerability
• [C-002] No VTY access-class — management plane exposed

⚠️ **HIGH:**
• [H-001] No OSPF authentication on Gi1
• [H-002] SNMP community 'public' with no ACL

_Full report attached as file →_
```

## Reply-Based Acknowledgment

Use reply messages to track alert status in the thread:

| Reply content / convention | Meaning |
|----------------------------|---------|
| "Acknowledged — someone is looking" | Alert acknowledged |
| "Fix in progress" | Work underway |
| "Resolved" | Resolved |
| "False positive / suppressed" | No action needed |
| "Waiting on change window" | Scheduled fix |
| "Escalated to team" | Escalated |

When a user replies acknowledging an alert, NetClaw can add a follow-up reply:
```
Acknowledged. Tracking in this thread.
```

## File Attachments

Attach supporting data via Webex file URLs:

- **Topology diagrams** — Draw.io PNG/SVG after topology discovery
- **Health reports** — Full text report as .txt attachment
- **Config diffs** — Pre/post change diffs
- **Log excerpts** — Relevant log sections for investigation
- **Markmap mind maps** — Protocol hierarchy visualizations

## Space Strategy

| Space | Purpose | Alert Types |
|-------|---------|-------------|
| NetClaw Alerts | Critical/High alerts only | Device down, security critical |
| NetClaw Reports | Scheduled reports | Health checks, audits, reconciliation |
| NetClaw General | General interaction | Ad-hoc queries, help |

## Message Threading (Replies)

- **Always use replies** for follow-up investigation steps under the original alert
- Post the initial alert as a top-level message in the space
- Post diagnostics, show command outputs, and resolution steps as replies to that message
- Post final resolution summary as both a reply and, if needed, a brief top-level update

## Integration with GAIT

Every alert should reference the GAIT session:

```
ℹ️ _Tracked in GAIT session `abc123` — commit `def456`_
```
