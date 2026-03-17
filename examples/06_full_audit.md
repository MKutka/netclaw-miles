# Example: Full Meraki Audit

## Prompt

```
Run a complete audit on my Meraki org — health, security, topology, vulnerabilities — and give me the full report
```

## What NetClaw Does

NetClaw chains Meraki and supporting skills for a full audit:

### Phase 1: Health (meraki-monitoring, meraki-network-ops)

- Device status per network: online/offline, last seen, uplink.
- Alerts: offline devices, uplink down, high latency.
- **Output:** Health summary with per-network status and alert list.

### Phase 2: Security (meraki-security-appliance)

- L3 firewall rules: order, default policy, logging.
- Security appliance settings and exposure.
- **Output:** Security posture summary and recommendations.

### Phase 3: Topology (meraki-network-ops)

- Org → networks → devices and uplink relationships.
- **Output:** Hierarchy and topology model for diagrams.

### Phase 4: Vulnerabilities (nvd-cve)

- Device firmware/software versions from Meraki (or Catalyst Center if used).
- Search NVD for CVEs by product/version.
- **Output:** CVE list by severity with mitigation notes.

### Phase 5: Visualization (optional)

- **drawio-diagram** — topology diagram from Phase 3.
- **markmap-viz** — org/network/device mind map.

### Phase 6: Final Report

NetClaw assembles a single report:

```
═══════════════════════════════════════════════════
  NetClaw Full Audit Report — Meraki
  Organization: Your Org
  Date: 2025-03-17
═══════════════════════════════════════════════════

1. HEALTH: 2 networks OK, 1 WARNING
   Main Office: 12/12 online
   Branch-A: 1 device offline (SW-2)
   Guest-WiFi: 5/5 APs online

2. SECURITY: Firewall default-deny, logging enabled
   Recommendations: [list from meraki-security-appliance]

3. TOPOLOGY: 3 networks, 18 devices
   [Summary of org → networks → devices]

4. VULNERABILITIES: [CVE summary by version from NVD]

═══════════════════════════════════════════════════
  PRIORITY ACTIONS
═══════════════════════════════════════════════════
  [List from health alerts + security + CVE]
```

## Skills Used

- **meraki-monitoring**, **meraki-network-ops** (health, topology)
- **meraki-security-appliance** (security)
- **nvd-cve** (vulnerabilities)
- **drawio-diagram**, **markmap-viz** (visualization)
- **gait-session-tracking** (audit trail)
