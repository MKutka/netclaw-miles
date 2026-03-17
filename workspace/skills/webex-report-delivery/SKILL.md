---
name: webex-report-delivery
description: "Deliver formatted network reports, audit results, topology diagrams, and compliance documentation to Webex spaces with markdown formatting"
user-invocable: true
metadata:
  { "openclaw": { "requires": { "bins": ["python3"] } } }
---

# Webex Report Delivery

Format and deliver comprehensive network engineering reports through Webex. This skill defines how to present health checks, security audits, topology discoveries, reconciliation results, and change reports using Webex markdown and reply threads.

## Webex Capabilities Used

| Capability | Purpose |
|------------|---------|
| Send to space | Post report messages to Webex spaces |
| Markdown | **Bold**, _italic_, tables, code blocks |
| Threaded replies | Detailed sections and follow-up content under the main report |
| File attachments | Attach full reports, diagrams via public URLs |

## Report Types and Formatting

### 1. Health Check Report

Post after running the pyats-health-check skill:

```
🩺 **Device Health Report — R1**
_2024-02-21 14:00 UTC_

**Device:** C8000V | IOS-XE 17.9.4a | Uptime: 47d 12h

| Check | Status | Details |
|-------|--------|---------|
| CPU (5min avg) | ✅ 12% | Normal |
| Memory | ⚠️ 78% | 2.0G / 2.6G — elevated |
| Interfaces | ✅ All up | 4/4 interfaces up/up |
| Hardware | ✅ OK | All modules operational |
| NTP | ✅ Synced | Offset 2ms to 10.0.0.1 |
| Logs | ⚠️ Events | 3 OSPF adjacency flaps |
| Connectivity | ✅ 100% | RTT 23ms to 8.8.8.8 |

**Overall:** ⚠️ **WARNING** — 2 items need attention
_Full report in reply thread →_
```

Thread the detailed per-section output as reply messages.

### 2. Security Audit Report

Post after running the pyats-security skill:

```
🛡️ **Security Audit Report — R1**
_2024-02-21 | IOS-XE 17.9.4a_

**Summary:** 2 CRITICAL | 2 HIGH | 3 MEDIUM | 2 LOW

**CRITICAL**
🔴 **C-001:** SSHv1 enabled — vulnerable to MITM attacks
> _Remediation:_ `ip ssh version 2`

🔴 **C-002:** No VTY access-class — management plane exposed to any source
> _Remediation:_ Apply `access-class MGMT-ACL in` on VTY lines

**HIGH**
🟠 **H-001:** No OSPF authentication on Gi1 — route injection risk
🟠 **H-002:** SNMP community 'public' with no source ACL

**MEDIUM**
🟡 M-001: No CoPP policy configured
🟡 M-002: HTTP server enabled (ip http server)
🟡 M-003: Console exec-timeout set to 0 0 (no timeout)

_Detailed findings with remediation commands in reply thread →_
```

### 3. Topology Discovery Report

Post after running the pyats-topology skill:

```
🗺️ **Topology Discovery — 2024-02-21**

**Devices Discovered:** 5 | **Links Mapped:** 12

**Discovery Sources:**
• CDP: 8 links (Cisco-to-Cisco)
• LLDP: 3 links (multi-vendor)
• OSPF: 4 routing adjacencies
• BGP: 2 peering sessions
• ARP: 23 L3 neighbors

**Adjacency Table:**
```
R1:Gi1 ←→ R2:Gi1     (10.1.1.0/30, OSPF Area 0)
R1:Gi2 ←→ SW1:Gi0/1  (10.1.2.0/24, Access VLAN 10)
R2:Gi2 ←→ SW2:Gi0/1  (10.2.1.0/24, Access VLAN 20)
R1:Gi3 ←→ ISP:eth0   (203.0.113.0/30, eBGP AS 65000)
```

_Draw.io diagram link and full topology model in reply thread →_
```

### 4. NetBox Reconciliation Report

Post after running the netbox-reconcile skill:

```
🔍 **NetBox Reconciliation Report — 2024-02-21**

**Scope:** 5 devices | 47 interfaces | 12 cables

| Category | Count | Details |
|----------|-------|---------|
| ✅ DOCUMENTED | 10 | Links match NetBox |
| ⚠️ UNDOCUMENTED | 1 | R1:Gi3 ↔ ISP:eth0 (not in NB) |
| 🔴 MISSING | 1 | NB cable #47 not seen by CDP |
| 🟠 MISMATCH | 0 | — |
| 🔵 IP DRIFT | 2 | R2:Lo0 differs from NetBox |

**Actions Required:**
1. Add R1:Gi3 ↔ ISP:eth0 cable to NetBox
2. Investigate missing NB cable #47 (R2:Gi3 ↔ SW3:Gi0/2)
3. Update R2 Loopback0 IP in NetBox (10.2.2.2 → 10.2.2.3)
```

### 5. Change Report

Post after running the pyats-config-mgmt skill:

```
✏️ **Change Report — R1**
_2024-02-21 14:30 UTC | ServiceNow CR: CHG0012345_

**Change:** Add Loopback99 for OSPF router-id migration

**Config Applied:**
```
interface Loopback99
 ip address 99.99.99.99 255.255.255.255
 description OSPF-RID-Migration
 no shutdown
```

**Verification:** ✅ **PASSED**
• Routing table: 47 → 48 routes (+1 connected 99.99.99.99/32)
• OSPF neighbors: 2 (FULL) — no change
• Connectivity: 100% to 8.8.8.8 — no change
• New log: %LINEPROTO-5-UPDOWN: Loopback99 up/up

**Rollback:** Not required
**CR Status:** Closed (successful)
```

### 6. Vulnerability Scan Report

Post after running the nvd-cve skill:

```
🐛 **Vulnerability Scan — Fleet Summary**
_2024-02-21 | NVD Database_

| Device | Software | CRITICAL | HIGH | MED | Action |
|--------|----------|----------|------|-----|--------|
| R1 | IOS-XE 17.9.4a | 2 | 3 | 5 | 🔴 URGENT |
| R2 | IOS-XE 17.12.1 | 0 | 1 | 2 | 🟡 PLAN |
| SW1 | IOS-XE 16.12.4 | 5 | 8 | 12 | 🔴 URGENT |

**Top Exposures:**
🔴 CVE-2023-20198 (CVSS 10.0) — Affects R1, SW1 — HTTP server exposed
🔴 CVE-2023-20273 (CVSS 7.2) — Affects R1 — Web UI accessible

_Detailed CVE analysis per device in reply thread →_
```

## Scheduled Report Delivery

NetClaw can deliver reports on a schedule when asked:

| Report | Suggested Cadence | Space |
|--------|------------------|-------|
| Fleet health check | Daily 06:00 UTC | NetClaw Reports |
| Security audit | Weekly (Monday) | NetClaw Reports |
| NetBox reconciliation | Weekly (Wednesday) | NetClaw Reports |
| Vulnerability scan | Monthly (1st) | NetClaw Reports |
| Topology validation | Monthly (15th) | NetClaw Reports |

## Formatting Guidelines

1. **Use emoji sparingly** — severity indicators only, not decoration
2. **Tables for structured data** — use markdown tables for alignment
3. **Reply threads for long output** — keep top-level messages concise (< 20 lines)
4. **Attach files for full reports** — text files for detailed output, images for diagrams
5. **Always include timestamps** — UTC, ISO format
6. **Link to ServiceNow CRs** — when changes are involved
7. **Reference GAIT sessions** — for audit trail continuity

## Comparison with Previous Reports

When delivering a new report, reference the previous one:

```
📈 **Health Trend — R1**
_Compared to last check (2024-02-20 14:00 UTC)_

| Metric | Previous | Current | Trend |
|--------|----------|---------|-------|
| CPU 5min | 10% | 12% | → Stable |
| Memory | 71% | 78% | ↑ +7% |
| Interfaces | 4/4 up | 4/4 up | → Stable |
| Ping RTT | 21ms | 23ms | → Stable |

⚠️ Memory trending upward — investigate if continues
```
