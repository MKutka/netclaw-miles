<p align="center">
  <img src="netclaw.jpg" alt="Netclaw-Miles — Meraki-focused CCIE-level AI agent" width="600">
</p>

# Netclaw-Miles

Netclaw-Miles is a CCIE-level AI network engineering coworker forked from [NetClaw](https://github.com/automateyournetwork/netclaw). It is an **advanced Meraki assistant** with 38 skills and 25 MCP server backends, focused on **enterprise networking and Cisco Meraki**. Built on [OpenClaw](https://github.com/openclaw/openclaw) with Anthropic Claude: ITSM gating, immutable audit trails, packet capture analysis, Cisco CML labs, Cisco SD-WAN vManage monitoring, Grafana observability, Cisco Meraki Dashboard management (~804 API endpoints), Cisco ThousandEyes, UML diagram generation, nmap and gtrace, Slack and Webex channel operations.

## About Netclaw-Miles

- **Credit:** This project is a fork of [NetClaw](https://github.com/automateyournetwork/netclaw) by [automateyournetwork](https://github.com/automateyournetwork). We retain most of the same features and functionality.
- **Focus:** Netclaw-Miles is an **advanced Meraki assistant** — oriented toward **Cisco Meraki** (Dashboard API, wireless, switching, security appliances, cameras, diagnostics) and the broader ecosystem (ISE, Catalyst Center, CML, ThousandEyes, SD-WAN, 25 MCP backends).
- **Miles:** The agent is **Miles**—a reincarnation of **Meraki Miles**, the former mascot of Meraki—embodying the same CCIE-level engineer persona with a Meraki-native identity.
- **Intentions:** To provide a Meraki-first, enterprise-ready variant of NetClaw for teams running cloud-managed networks, with the same safety rules, GAIT audit trail, and change-management practices as upstream.

---

## Quick Install

Clone this repository (Netclaw-Miles) and run the installer.

```bash
git clone https://github.com/MKutka/netclaw-miles.git
cd netclaw-miles
./scripts/install.sh          # installs everything, then launches the setup wizard
```

To install the original [NetClaw](https://github.com/automateyournetwork/netclaw) instead, use `https://github.com/automateyournetwork/netclaw.git` and `cd netclaw`.

That's it. The installer clones 25 MCP servers, deploys 38 skills, then launches a two-phase setup:

**Phase 1: `openclaw onboard`** (OpenClaw's built-in wizard)
- Pick your AI provider (Anthropic, OpenAI, Bedrock, Vertex, 30+ options)
- Set up the gateway (local mode, auth, port)
- Connect channels (Slack, Discord, Telegram, WhatsApp, etc.)
- Install the daemon service

**Phase 2: `./scripts/setup.sh`** (Netclaw-Miles platform credentials)
- Platform credentials (ServiceNow, ISE, Catalyst Center, NVD, CML, Meraki, ThousandEyes, SD-WAN, Grafana, Prometheus)
- Your identity (name, role, timezone for USER.md)

After setup, start NetClaw:

```bash
openclaw gateway              # terminal 1
openclaw chat --new           # terminal 2
```

Reconfigure anytime:
- `openclaw configure` — AI provider, gateway, channels
- `./scripts/setup.sh` — network platform credentials

### Enabling Webex

To use Webex alongside Slack, install the [@jimiford/webex](https://github.com/JimiHFord/openclaw-webex) OpenClaw channel plugin and configure it.

1. **Install the plugin** (where OpenClaw can load it):
   ```bash
   npm install @jimiford/webex
   ```
   If OpenClaw is installed globally, ensure the package is available to the gateway (e.g. install in the same Node environment or add to `plugins.load.paths` as `node_modules/@jimiford/webex`).

2. **Configure OpenClaw** with the Webex channel. In your OpenClaw config (e.g. under `~/.openclaw/` or as specified by `openclaw configure`), add:
   ```json
   {
     "channels": {
       "webex": {
         "enabled": true,
         "token": "YOUR_BOT_TOKEN",
         "webhookUrl": "https://your-domain.com/webhooks/webex",
         "webhookSecret": "YOUR_STRONG_RANDOM_SECRET",
         "dmPolicy": "allowlisted",
         "allowFrom": ["your-webex-person-id", "your-email@example.com"]
       }
     },
     "plugins": {
       "load": {
         "paths": ["node_modules/@jimiford/webex"]
       }
     }
   }
   ```
   Use **allowlisted** in production; list person IDs or emails in `allowFrom`. Never commit tokens or secrets.

3. **Environment variables** — Put secrets in `~/.openclaw/.env` (do not commit):
   ```
   WEBEX_BOT_TOKEN=your_bot_access_token
   WEBHOOK_URL=https://your-domain.com/webhooks/webex
   WEBHOOK_SECRET=your_webhook_secret
   ```
   Generate a webhook secret with `openssl rand -hex 32`.

4. **Webhook URL** must be publicly reachable over HTTPS. For local testing use a tunnel (e.g. ngrok). After starting the gateway, register webhooks with Webex (the plugin may do this automatically; see [openclaw-webex](https://github.com/JimiHFord/openclaw-webex) docs).

5. **Netclaw-Miles** does not implement the Webex transport; it relies on OpenClaw's plugin system. The workspace skills (`webex-network-alerts`, `webex-report-delivery`, `webex-incident-workflow`, `webex-user-context`) define how the agent formats and behaves in Webex. See [TOOLS.md](TOOLS.md) for Webex spaces and [docs/webex-checklist.md](docs/webex-checklist.md) for a verification checklist.

---

## What It Does

NetClaw is an autonomous network engineering agent powered by Claude that can:

- **Manage** Cisco Meraki infrastructure via Dashboard API (~804 endpoints) — org inventory, networks, devices, wireless SSIDs, RF profiles, switch ports, VLANs, MX firewall rules, site-to-site VPN, content filtering, security events, camera analytics, live diagnostics (ping, cable test), and configuration change audit
- **Monitor** Meraki device health and status — device online/offline, uplink status, live ping and cable test, config change audit — fleet-wide via Dashboard API and action batches
- **Troubleshoot** Meraki networks using meraki-monitoring, meraki-switch-ops, meraki-wireless-ops, and meraki-security-appliance — connectivity, client issues, VPN, port and RF diagnostics
- **Configure** Meraki networks with full ITSM-gated change management — ServiceNow CR, Dashboard API or action batches, verify via Meraki MCP, GAIT audit
- **Investigate** endpoints via ISE — auth history, posture, profiling, human-authorized quarantine
- **Monitor** network paths via Cisco ThousandEyes — synthetic test results, agent health, hop-by-hop path visualization (latency, loss, MPLS labels per hop), BGP route analysis (AS path, reachability, origin validation), outage investigation, anomaly detection, instant on-demand tests, endpoint VPN diagnostics, and AI-powered views explanations — via both community (9 tools, local stdio) and official (~20 tools, remote HTTP) MCP servers
- **Monitor** Cisco SD-WAN fabric via vManage API (read-only) — device inventory (vManage, vSmart, vBond, vEdge), WAN Edge serial/chassis details, device and feature templates, centralized policies, active alarms, audit events, interface statistics, BFD session health, OMP route analysis, DTLS/TLS control connections, and running config retrieval
- **Observe** infrastructure via Grafana (75+ tools) — search/view/modify dashboards, query Prometheus metrics with PromQL (interface traffic, CPU, BGP state, error rates, histogram percentiles), query Loki logs with LogQL (syslog, SNMP traps, application logs), manage alerting rules and contact points, track incidents with timeline activities, view OnCall schedules and current responders, annotate dashboards, render panel images, and generate deep links
- **Query** Prometheus directly (6 tools) — execute instant and range PromQL queries, browse available metrics with pagination, retrieve metric metadata (type, help, unit), inspect scrape target health and status, and verify Prometheus server availability. Supports basic auth, bearer tokens (Grafana Cloud, Thanos, Cortex), and multi-tenant org IDs
- **Trace** network paths with gtrace — advanced traceroute with MPLS label detection, ECMP path discovery, and NAT detection; continuous MTR monitoring with per-hop loss and jitter; distributed GlobalPing probes from 500+ worldwide locations; ASN ownership lookup, geolocation, and reverse DNS for IP enrichment
- **Scan** for CVE vulnerabilities against the NVD database with CVSS severity correlation and exposure confirmation
- **Operate** Catalyst Center — device inventory, client monitoring, site management, and troubleshooting
- **Calculate** IPv4 and IPv6 subnets — VLSM planning, wildcard masks, allocation standards
- **Alert** via Slack — severity-formatted notifications, incident workflows, and user-aware routing
- **Diagram** your network with Draw.io topology maps (color-coded by reconciliation status)
- **Visualize** protocol hierarchies as interactive Markmap mind maps
- **Generate** UML and infrastructure diagrams via Kroki — 27+ types including network topology (nwdiag), rack layouts (rackdiag), packet headers (packetdiag), protocol state machines, sequence diagrams, C4 architecture, Mermaid, D2, Graphviz, ERD — output as SVG, PNG, PDF
- **Reference** IETF RFCs and Wikipedia for standards-compliant configuration
- **Analyze** packet captures — upload a pcap to Slack and NetClaw runs deep tshark analysis: protocol hierarchy, conversations, endpoints, DNS/HTTP extraction, expert info, and filtered inspection
- **Simulate** network topologies in Cisco CML — create labs, add nodes, wire links, start/stop labs, execute CLI commands, capture packets on lab links, and manage CML users — all from natural language via Slack
- **Audit** every action in an immutable Git-based trail (GAIT) — there is always an answer to "what did the AI do and why"

---

## Architecture

```
Human (Slack / Webex / WebChat) --> NetClaw (CCIE Agent on OpenClaw)
                                |
                                |-- DEVICE AUTOMATION:
                                |     MCP: Cisco Meraki       --> Dashboard API (~804 endpoints): wireless, switching, security, cameras, diagnostics
                                |     MCP: Catalyst Center    --> DNA-C API (devices, clients, sites)
                                |
                                |-- INFRASTRUCTURE:
                                |     MCP: Cisco ISE       --> Identity, posture, TrustSec
                                |     MCP: ServiceNow      --> Incidents, Changes, CMDB
                                |
                                |-- SECURITY & COMPLIANCE:
                                |     MCP: NVD CVE         --> NIST vulnerability database
                                |
                                |-- PACKET ANALYSIS:
                                |     MCP: Packet Buddy     --> pcap/pcapng deep analysis via tshark
                                |
                                |-- NETWORK INTELLIGENCE:
                                |     MCP: ThousandEyes (community) --> Tests, agents, path vis, dashboards (9 tools, stdio)
                                |     MCP: ThousandEyes (official)  --> Alerts, outages, BGP, instant tests, endpoints (~20 tools, remote HTTP)
                                |
                                |-- SD-WAN:
                                |     MCP: Cisco SD-WAN      --> vManage API (12 read-only tools: devices, templates, policies, alarms)
                                |
                                |-- OBSERVABILITY:
                                |     MCP: Grafana            --> Dashboards, Prometheus, Loki, alerting, incidents, OnCall (75+ tools via uvx)
                                |     MCP: Prometheus         --> Direct PromQL queries, metric discovery, target health (6 tools via pip)
                                |
                                |-- LAB & SIMULATION:
                                |     MCP: Cisco CML         --> Lab lifecycle, topology, nodes, captures
                                |
                                |-- UTILITIES:
                                |     MCP: Subnet Calc     --> IPv4 + IPv6 CIDR calculator
                                |     MCP: GAIT            --> Git-based AI audit trail
                                |     MCP: Wikipedia       --> Technology context
                                |     MCP: Markmap         --> Mind map visualizations
                                |     MCP: Draw.io         --> Network topology diagrams
                                |     MCP: UML MCP         --> 27+ diagram types via Kroki
                                '     MCP: RFC Lookup      --> IETF standards reference
```

---

## OpenClaw Workspace Files

Netclaw-Miles ships with the full set of OpenClaw workspace markdown files. These are injected into the agent's system prompt at session start to define its identity, behavior, and operating procedures. In this fork, [SOUL.md](SOUL.md) defines the agent as **Miles** with a Meraki-focused persona.

| File | Purpose | Loaded When |
|------|---------|-------------|
| **[SOUL.md](SOUL.md)** | Core personality (Miles), CCIE expertise, 12 non-negotiable rules, protocol knowledge base | Every session |
| **[AGENTS.md](AGENTS.md)** | Operating instructions: memory system, safety rules, change management workflow, Slack behavior, escalation matrix | Every session |
| **[IDENTITY.md](IDENTITY.md)** | Name, creature type, vibe, emoji — identity card | Every session |
| **[USER.md](USER.md)** | Your preferences, timezone, role, network details — personalization layer (edit this) | Every session |
| **[TOOLS.md](TOOLS.md)** | Local infrastructure notes: device IPs, SSH hosts, Slack channels, site info (edit this) | Every session |
| **[HEARTBEAT.md](HEARTBEAT.md)** | Periodic health checks: device reachability, OSPF/BGP state, CPU/memory, syslog scan | Every heartbeat cycle |

**How they work:** OpenClaw reads these files at session start and injects them under "Project Context" in the system prompt. Each file is capped at 20,000 characters. Sub-agents only receive AGENTS.md and TOOLS.md.

**What to customize:** Edit `USER.md` with your name, timezone, and preferences. Edit `TOOLS.md` with your device IPs, Slack channels, and site information. The rest define the agent's behavior and expertise — modify only if you want to change how it operates.

---

## MCP Servers (25)

| # | MCP Server | Repository | Transport | Function |
|---|------------|------------|-----------|----------|
| 1 | Cisco Meraki | [CiscoDevNet/meraki-magic-mcp-community](https://github.com/CiscoDevNet/meraki-magic-mcp-community) | stdio (Python) | Meraki Dashboard API — ~804 endpoints: orgs, networks, wireless, switching, security, cameras, diagnostics |
| 2 | Catalyst Center | [richbibby/catalyst-center-mcp](https://github.com/richbibby/catalyst-center-mcp) | stdio (Python) | DNA-C API — devices, clients, sites, interfaces |
| 3 | Cisco ISE | [automateyournetwork/ISE_MCP](https://github.com/automateyournetwork/ISE_MCP) | stdio (Python) | Identity policy, posture, TrustSec, endpoint control |
| 4 | ServiceNow | [echelon-ai-labs/servicenow-mcp](https://github.com/echelon-ai-labs/servicenow-mcp) | stdio (Python) | Incidents, change requests, CMDB |
| 5 | Packet Buddy | Built-in | stdio (Python) | pcap/pcapng deep analysis via tshark — upload pcaps to Slack |
| 6 | Cisco CML | [xorrkaz/cml-mcp](https://github.com/xorrkaz/cml-mcp) | stdio (Python) | Lab lifecycle, topology, nodes, links, captures, CLI exec, admin |
| 7 | ThousandEyes (community) | [CiscoDevNet/thousandeyes-mcp-community](https://github.com/CiscoDevNet/thousandeyes-mcp-community) | stdio (Python) | Tests, agents, path visualization, dashboards, users, account groups (9 read-only tools) |
| 8 | ThousandEyes (official) | [CiscoDevNet/ThousandEyes-MCP-Server-official](https://github.com/CiscoDevNet/ThousandEyes-MCP-Server-official) | Remote HTTP | Alerts, outages, BGP routes, instant tests, endpoint agents, anomalies, AI views (~20 tools) |
| 9 | NVD CVE | [marcoeg/mcp-nvd](https://github.com/marcoeg/mcp-nvd) | stdio (Python) | NIST NVD vulnerability database with CVSS scoring |
| 22 | Subnet Calculator | [automateyournetwork/GeminiCLI_SubnetCalculator_Extension](https://github.com/automateyournetwork/GeminiCLI_SubnetCalculator_Extension) | stdio (Python) | IPv4 + IPv6 CIDR subnet calculator |
| 33 | GAIT | [automateyournetwork/gait_mcp](https://github.com/automateyournetwork/gait_mcp) | stdio (Python) | Git-based AI tracking and audit |
| 34 | Wikipedia | [automateyournetwork/Wikipedia_MCP](https://github.com/automateyournetwork/Wikipedia_MCP) | stdio (Python) | Standards and technology context |
| 35 | Markmap | [automateyournetwork/markmap_mcp](https://github.com/automateyournetwork/markmap_mcp) | stdio (Node) | Hierarchical mind map generation |
| 36 | Draw.io | [@drawio/mcp](https://github.com/jgraph/drawio-mcp) | npx | Network topology diagram generation |
| 37 | RFC Lookup | [@mjpitz/mcp-rfc](https://github.com/mjpitz/mcp-rfc) | npx | IETF RFC search and retrieval |
| 38 | UML MCP | [antoinebou12/uml-mcp](https://github.com/antoinebou12/uml-mcp) | stdio (Python) | 27+ UML/diagram types via Kroki — class, sequence, nwdiag, rackdiag, packetdiag, C4, Mermaid, D2, Graphviz, ERD, BPMN (2 tools) |
| 14 | Cisco SD-WAN | [siddhartha2303/cisco-sdwan-mcp](https://github.com/siddhartha2303/cisco-sdwan-mcp) | stdio (Python) | vManage read-only monitoring — fabric devices, WAN Edge inventory, templates, policies, alarms, BFD, OMP routes, control connections (12 tools) |
| 42 | Grafana | [grafana/mcp-grafana](https://github.com/grafana/mcp-grafana) | uvx (Go) | Observability platform — dashboards, Prometheus PromQL, Loki LogQL, alerting, incidents, OnCall, annotations, panel rendering (75+ tools) |
| 43 | Prometheus | [pab1it0/prometheus-mcp-server](https://github.com/pab1it0/prometheus-mcp-server) | stdio (Python) | Direct PromQL monitoring — instant/range queries, metric discovery with pagination, metric metadata, scrape target health, system health check (6 tools) |
| 23 | nmap | [sbmilburn/nmap-mcp](https://github.com/sbmilburn/nmap-mcp) | stdio (Python) | Network scanning — host discovery, SYN/TCP/UDP port scanning, service/OS detection, NSE scripts, vuln scanning with CIDR allowlist + audit logging (14 tools) |
| 24 | gtrace | [hervehildenbrand/gtrace](https://github.com/hervehildenbrand/gtrace) | stdio (Go binary) | Advanced traceroute (MPLS/ECMP/NAT detection), MTR continuous monitoring, GlobalPing distributed probes (500+ locations), ASN lookup, geolocation, reverse DNS (6 tools) |

All MCP servers communicate via stdio (JSON-RPC 2.0) through `scripts/mcp-call.py`. CML MCP is pip-installed (`cml-mcp`). Meraki Magic MCP runs via FastMCP stdio (~804 Dashboard API endpoints). ThousandEyes community MCP runs via stdio (9 read-only tools); ThousandEyes official MCP is a remote HTTP endpoint hosted by Cisco at `https://api.thousandeyes.com/mcp` (~20 tools via `npx mcp-remote`). UML MCP runs via stdio (2 tools for 27+ diagram types via Kroki multi-engine rendering). SD-WAN MCP runs via stdio (12 read-only tools for Cisco SD-WAN vManage fabric monitoring). Grafana MCP runs via `uvx mcp-grafana` (75+ tools for dashboards, Prometheus, Loki, alerting, incidents, OnCall). Prometheus MCP is pip-installed (`prometheus-mcp-server`) and runs via stdio (6 tools for direct PromQL queries, metric discovery, and scrape target health). nmap MCP runs via FastMCP stdio (14 tools for host discovery, port scanning, service/OS detection, NSE scripts, and vulnerability scanning with CIDR scope enforcement and audit logging). gtrace MCP runs via `gtrace mcp` stdio (6 tools for advanced traceroute with MPLS/ECMP/NAT detection, MTR continuous monitoring, GlobalPing distributed probes, ASN lookup, geolocation, and reverse DNS). No persistent connections, no port management.

---

## Skills (38)

### Cisco Meraki Skills (5)

| Skill | What It Does |
|-------|-------------|
| **meraki-network-ops** | Meraki Dashboard organization and network management via ~804 API endpoints: list organizations, org inventory, license status, network CRUD, device lifecycle (claim, unclaim, reboot), client discovery with usage/policy, uplink status, config change audit, action batches for bulk operations, and generic `call_meraki_api` for any Dashboard endpoint. Built-in caching (50-90% API reduction), auto-retry, rate limit handling. |
| **meraki-wireless-ops** | Meraki wireless management: list/update SSIDs (auth, VLAN, band, splash), RF profile creation with band selection and power settings, channel utilization analysis per AP, signal quality (SNR) monitoring, connection statistics (auth/DHCP success rates), per-client connectivity event investigation (roaming, deauth, failures). Workflows for wireless health, client troubleshooting, and RF optimization. |
| **meraki-switch-ops** | Meraki MS switch operations: port configuration (VLAN, type, PoE, BPDU guard, RSTP), live port statuses (speed, duplex, CRC errors, traffic, PoE draw), VLAN management (list, create), switch ACLs, QoS rules, and port cycling for PoE resets. Workflows for port audit, VLAN provisioning, and port troubleshooting. |
| **meraki-security-appliance** | Meraki MX security appliance: L3 outbound firewall rules (audit and modify), site-to-site Auto VPN (status, hub/spoke config), content filtering (URL categories, blocked/allowed lists), traffic shaping (global and per-rule bandwidth limits), IDS/IPS security event investigation. Workflows for firewall audit, VPN troubleshooting, content filter review, and security incident response. |
| **meraki-monitoring** | Meraki live diagnostics and monitoring: ping from device (latency, loss, jitter), cable test on switch ports (OK/open/short/length), LED blink for physical identification, wake-on-LAN, MV camera analytics (live person/vehicle counts, zones, snapshots, Sense ML detection), config change tracking (who changed what, when), API request history, webhook delivery logs. |

### Domain Skills (4)

| Skill | What It Does |
|-------|-------------|
| **ise-posture-audit** | ISE audit: authorization policy review (default-allow detection), posture compliance assessment, profiling coverage analysis, TrustSec SGT matrix analysis (permit-all detection), active session health. |
| **ise-incident-response** | Endpoint investigation: lookup by MAC/IP/username, auth history, posture/profile review, risk assessment. **Human decision point required** before any quarantine action. ServiceNow Security Incident creation. GAIT audit. |
| **servicenow-change-workflow** | Full ITSM lifecycle: pre-change incident check, CR creation, approval gate, execution coordination, post-change verification, rollback procedure, CR closure/escalation. Supports Normal, Standard, and Emergency change types. |
| **gait-session-tracking** | Mandatory Git-based audit trail. Session branch creation, turn recording (prompt/response/artifacts), session log display. 9 GAIT tools: status, init, branch, checkout, record_turn, log, show, pin, summarize_and_squash. |

### Catalyst Center Skills (3)

| Skill | What It Does |
|-------|-------------|
| **catc-inventory** | Device inventory via Catalyst Center: filter by hostname/IP/platform/role/reachability, site hierarchy, interface details. |
| **catc-client-ops** | Client monitoring: wired/wireless clients, filter by SSID/band/site/OS, client details by MAC, count analytics, time-based trending. |
| **catc-troubleshoot** | CatC troubleshooting: device unreachable, client connectivity, interface down, site-wide outage triage. |

### Packet Analysis Skills (1)

| Skill | What It Does |
|-------|-------------|
| **packet-analysis** | Deep pcap analysis via tshark. Upload a `.pcap` or `.pcapng` file to Slack and NetClaw analyzes it: protocol hierarchy, IP/TCP/UDP conversations, top endpoints, DNS queries, HTTP requests, expert info (retransmissions, errors), filtered packet inspection, and full JSON decode. 12 MCP tools for comprehensive L2-L7 packet investigation. |

### nmap Network Scanning Skills (3)

| Skill | What It Does |
|-------|-------------|
| **nmap-network-scan** | Host discovery and port scanning — ICMP/ARP host discovery, SYN/TCP/UDP port scanning on authorized networks. CIDR scope enforcement and audit logging. 6 tools for subnet discovery and targeted port scanning. |
| **nmap-service-detection** | Service fingerprinting, OS detection, NSE scripts, and vulnerability scanning. Full recon sweeps combining SYN scan + service detection + OS fingerprinting + default NSE scripts. 5 tools for security assessment. |
| **nmap-scan-management** | Custom nmap scans with arbitrary flags (scope-enforced), scan history listing, and result retrieval by ID. Before/after comparison workflows for change validation. 3 tools. |

### gtrace Path Analysis & IP Enrichment Skills (2)

| Skill | What It Does |
|-------|-------------|
| **gtrace-path-analysis** | Advanced traceroute with MPLS label detection, ECMP path discovery, and NAT translation detection. Continuous MTR monitoring with per-hop loss%, jitter, and latency stats. Distributed GlobalPing probes from 500+ worldwide locations for global path comparison. 3 tools for comprehensive network path troubleshooting. |
| **gtrace-ip-enrichment** | IP address enrichment: ASN ownership lookup (AS number, organization, network CIDR, RIR), geolocation (city, region, country, coordinates, timezone), and reverse DNS resolution (PTR records). Use to enrich traceroute hops, investigate unknown IPs, or verify BGP peer identity. 3 tools. |

### Cisco CML Skills (5)

| Skill | What It Does |
|-------|-------------|
| **cml-lab-lifecycle** | Full lab lifecycle: create, start, stop, wipe, delete, clone, import/export CML labs. Build labs from natural language descriptions ("build me a 3-router OSPF lab"). Export topologies as YAML for sharing or GitHub commits. |
| **cml-topology-builder** | Build topologies: add nodes (IOSv, NX-OS, IOS-XR, ASAv, servers), create interfaces, wire links, set link conditioning (bandwidth, latency, jitter, loss for WAN simulation), control link states (up/down for failure simulation), add visual annotations (text, rectangles, ellipses, lines). Grid-based layout. |
| **cml-node-operations** | Node operations: start/stop individual nodes, set startup configs (IOS, NX-OS, IOS-XR templates), execute CLI commands, retrieve console logs for troubleshooting, download running configs, wipe and reconfigure nodes. |
| **cml-packet-capture** | Capture packets on CML lab links: start/stop captures with BPF filters, download pcap files, and hand off to Packet Buddy for deep tshark analysis. Protocol-specific capture workflows for BGP, OSPF, STP, ICMP troubleshooting. |
| **cml-admin** | CML server administration: user/group management, system info (CPU, RAM, disk), licensing status, resource usage monitoring, capacity planning for new labs. |

### Cisco SD-WAN Skills (1)

| Skill | What It Does |
|-------|-------------|
| **sdwan-ops** | Cisco SD-WAN vManage read-only operations (12 tools): fabric device inventory (vManage, vSmart, vBond, vEdge), WAN Edge details (serial, chassis ID), device and feature templates, centralized policy definitions, active alarms, audit events, interface statistics per device, BFD session status, OMP routes (received/advertised), DTLS/TLS control connections, running configuration retrieval. All operations are read-only — no configuration changes possible. GAIT audit trail. |

### Grafana Observability Skills (1)

| Skill | What It Does |
|-------|-------------|
| **grafana-observability** | Grafana observability platform (75+ tools): dashboard search/summary/property extraction/modification, Prometheus PromQL queries (instant/range, metric discovery, histogram percentiles), Loki LogQL queries (log search, label discovery, patterns, stats), alerting rules (list/create/update/delete, contact points), incident management (list/create/update, activity timeline), OnCall schedules (rotations, current on-call, alert groups), annotations, panel image rendering, deep link generation, and Sift investigation (error patterns, slow requests). Runs via `uvx mcp-grafana` (Go). Supports both self-hosted Grafana and Grafana Cloud. Read-only mode available (`--disable-write`). Dashboard and alert modifications gated by ServiceNow CR. GAIT audit trail. |

### Prometheus Monitoring Skills (1)

| Skill | What It Does |
|-------|-------------|
| **prometheus-monitoring** | Direct Prometheus access (6 tools): execute instant PromQL queries (`execute_query`), execute range queries with time intervals (`execute_range_query`), browse available metrics with pagination (`list_metrics`), retrieve metric type/help/unit metadata (`get_metric_metadata`), view scrape target details and health (`get_targets`), check Prometheus server availability (`health_check`). Supports basic auth, bearer token (Grafana Cloud, Thanos, Cortex), multi-tenant org ID, SSL control, and custom headers. Installed via `pip3 install prometheus-mcp-server`. Complementary to grafana-observability for direct PromQL access without dashboard overhead. GAIT audit trail. |

### ThousandEyes Skills (2)

| Skill | What It Does |
|-------|-------------|
| **te-network-monitoring** | ThousandEyes network monitoring via two MCP servers — community (9 tools, local stdio) for core monitoring: list tests, agents, test results, path visualization, dashboards, dashboard widgets, users, account groups; and official (~20 tools, remote HTTP) for advanced analysis: alerts, events, outages, instant tests, anomalies, metrics, AI-powered views explanations, endpoint agents, BGP results, path visualization. Workflows for network performance assessment, path troubleshooting, outage investigation, endpoint experience, and BGP monitoring. |
| **te-path-analysis** | Deep network path analysis and active troubleshooting via ThousandEyes — hop-by-hop path visualization (IP, DNS name, latency, packet loss, MPLS labels, network owner per hop), BGP route analysis (AS path, origin AS, prefix reachability, route stability from 300+ global BGP monitors), outage investigation (scope, timeline, affected services), instant on-demand tests (use judiciously — consumes test units), endpoint VPN diagnostics (WiFi signal, DNS, VPN latency), and anomaly detection. Workflows for "Why is site X slow?", internet outage triage, endpoint VPN troubleshooting, and BGP hijack/leak detection. |

### Reference & Utility Skills (7)

| Skill | Tool Backend | Purpose |
|-------|-------------|---------|
| **nvd-cve** | [marcoeg/mcp-nvd](https://github.com/marcoeg/mcp-nvd) (Python) | NVD vulnerability database — search by keyword, get CVE details with CVSS v3.1/v2.0 scores, exposure correlation |
| **subnet-calculator** | [SubnetCalculator MCP](https://github.com/automateyournetwork/GeminiCLI_SubnetCalculator_Extension) | IPv4 + IPv6 subnet calculator — VLSM planning, wildcard masks, address classification, RFC 6164 /127 links |
| **wikipedia-research** | [Wikipedia_MCP](https://github.com/automateyournetwork/Wikipedia_MCP) | Protocol history, standards evolution, technology context. 6 tools: search, summary, content, references, categories, exists check. |
| **markmap-viz** | [markmap-mcp](https://github.com/automateyournetwork/markmap_mcp) (Node) | Interactive mind maps from markdown — OSPF area hierarchies, BGP peer trees, drift summaries |
| **drawio-diagram** | [@drawio/mcp](https://github.com/jgraph/drawio-mcp) (npx + [official skill-cli](https://github.com/jgraph/drawio-mcp/tree/main/skill-cli)) | Network topology diagrams — native `.drawio` files with CLI export (PNG/SVG/PDF with embedded XML), plus browser-based Mermaid/XML/CSV via MCP server. Color-coded by reconciliation status. |
| **rfc-lookup** | [@mjpitz/mcp-rfc](https://github.com/mjpitz/mcp-rfc) (npx) | IETF RFC search, retrieval, and section extraction — BGP (4271), OSPF (2328), NTP (5905) |
| **uml-diagram** | [UML MCP](https://github.com/antoinebou12/uml-mcp) (stdio) | 27+ UML/diagram types via Kroki — class, sequence, nwdiag, rackdiag, packetdiag, C4, Mermaid, D2, Graphviz, ERD, BPMN |

### Slack Integration Skills (4)

| Skill | Purpose |
|-------|---------|
| **slack-network-alerts** | Severity-formatted alert delivery (CRITICAL/HIGH/WARNING/INFO), reaction-based acknowledgment, fleet summary posts |
| **slack-report-delivery** | Rich Slack formatting for health checks, security audits, topology maps, reconciliation results, change reports |
| **slack-incident-workflow** | Full incident lifecycle in Slack: declaration, triage, automated investigation, status updates, resolution, post-incident review |
| **slack-user-context** | User-aware interactions: DND-respecting escalation, timezone-aware scheduling, role-based response depth, shift handoff summaries |

### Webex Integration Skills (4)

| Skill | Purpose |
|-------|---------|
| **webex-network-alerts** | Severity-formatted alert delivery in Webex spaces, reply-thread follow-up, fleet summaries |
| **webex-report-delivery** | Markdown reports for health checks, security audits, topology maps, reconciliation, change reports |
| **webex-incident-workflow** | Incident lifecycle in Webex: declaration, triage, investigation, status updates, resolution, PIR |
| **webex-user-context** | Allowlist-aware escalation, space-based routing, role-based response depth |

---

## Network Intelligence

### Cisco ThousandEyes

**2 MCP servers, 2 skills** — network monitoring and deep path analysis.

#### Key Difference: Two Complementary MCP Servers

ThousandEyes uses **two MCP servers** that share a single API token:

| MCP Server | Transport | Tools | Strength |
|---|---|---|---|
| Community (`thousandeyes-mcp-community`) | stdio (Python, local) | 9 read-only tools | Core monitoring: tests, agents, path vis, dashboards |
| Official (`api.thousandeyes.com/mcp`) | Remote HTTP (Cisco-hosted) | ~20 tools | Advanced: alerts, outages, BGP, instant tests, endpoint agents, AI |

The community server runs locally via stdio. The official server is a remote HTTP endpoint hosted by Cisco — no local install, accessed via `npx mcp-remote` with Bearer token authentication.

#### Credentials

| Variable | Example | Description |
|----------|---------|-------------|
| `TE_TOKEN` | `eyJhbGci...` | ThousandEyes API v7 OAuth bearer token (used by both servers) |

Get your token from: **ThousandEyes > Account Settings > Users & Roles > OAuth Bearer Token**

Run `./scripts/setup.sh` — the wizard prompts for the token.

#### What You Can Do

| Skill | Capabilities |
|-------|-------------|
| `te-network-monitoring` | List tests/agents, query test results, path visualization, dashboard widgets, alerts, events, outages, endpoint agents, anomalies, AI views explanations |
| `te-path-analysis` | Hop-by-hop path analysis (latency, loss, MPLS per hop), BGP route analysis (AS paths, prefix reachability from 300+ global monitors), outage investigation, instant on-demand tests, endpoint VPN diagnostics, BGP hijack/leak detection |

#### Prerequisites

- Cisco ThousandEyes account with API v7 access
- Python 3.12+ (for community MCP server)
- Node.js / npx (for official MCP server via `mcp-remote`)
- Org must not be opted out of ThousandEyes AI features (for official server)

#### Cost Notes

- **Community server tools**: Read-only, no additional cost beyond normal API usage
- **Instant Tests** (official server): Consume ThousandEyes test units — use judiciously
- **API rate limits**: Queries count against your org's ThousandEyes API rate limit

---

## How Skills Work

Each skill is a `SKILL.md` file with YAML frontmatter and markdown instructions. OpenClaw loads them into the agent context at session start.

```markdown
---
name: meraki-monitoring
description: "Meraki live diagnostics and device monitoring..."
user-invocable: true
metadata:
  { "openclaw": { "requires": { "bins": ["python3"], "env": ["MERAKI_API_KEY"] } } }
---

# Meraki Monitoring

(Step-by-step procedures, Dashboard API usage, live ping/cable test,
 report templates — everything the agent needs to work autonomously)
```

The `metadata.openclaw.requires` block declares binary and environment variable dependencies. The markdown body is the agent's playbook.

Every tool call goes through `scripts/mcp-call.py`, which handles MCP JSON-RPC protocol: initialize, notify, tool call, terminate. No persistent server connections, no port management.

```
python3 mcp-call.py "<server-command>" <tool-name> '<arguments-json>'
```

---

## Standard Workflows

### Meraki Health / Device Status
```
meraki-network-ops + meraki-monitoring
--> getOrganizations / getOrganizationInventory: list devices
--> getDeviceStatus, getDeviceUplink: online/offline, uplink health
--> createDeviceLiveToolsPing, createDeviceLiveToolsCableTest: live diagnostics
--> Severity: device offline, uplink down, cable faults
--> GAIT audit trail
```

### Configuration Change (Meraki)
```
servicenow-change-workflow + Meraki Dashboard API
--> Pre-check: no open P1/P2 on affected CIs
--> ServiceNow CR created, approved
--> Meraki API or action batches: apply config changes
--> Verify via Meraki MCP (device status, config change audit)
--> ServiceNow CR closed
--> GAIT full session audit
```

### Meraki Security Appliance Audit
```
meraki-security-appliance
--> getNetworkSecurityFirewallRules: L3 outbound rules
--> getNetworkSecurityContentFiltering: URL categories
--> getNetworkSecuritySecurityEvents: IDS/IPS events
--> ISE: verify device registered as NAD (where applicable)
--> NVD CVE: software version scan for Meraki firmware (CVSS >= 7.0)
--> GAIT commit
```

### Endpoint Incident Response
```
ise-incident-response
--> Endpoint lookup by MAC/IP/username
--> Auth history, posture, profile review
--> Human decision point
--> [If authorized] ISE quarantine
--> ServiceNow Security Incident
--> GAIT audit trail
```

### Meraki Network / Topology
```
meraki-network-ops
--> getOrganizations, getOrganizationNetworks, getOrganizationInventory
--> Device-to-network mapping, uplink status
--> Draw.io diagram from Meraki network/device data
--> Markmap for hierarchy
--> GAIT commit
```

### Catalyst Center Client Investigation
```
catc-client-ops + catc-troubleshoot
--> Client lookup by MAC address
--> Connection details: SSID, band, AP, VLAN, health score
--> GAIT audit
```

### Packet Capture Analysis (Slack Upload)
```
packet-analysis
--> User uploads .pcap file to Slack channel
--> NetClaw downloads and saves the file
--> pcap_summary: packet count, duration, capture size
--> pcap_protocol_hierarchy: protocol breakdown (TCP 45%, UDP 30%, DNS 15%...)
--> pcap_conversations: who talked to whom (IP pairs, byte counts)
--> pcap_expert_info: retransmissions, RSTs, errors flagged by tshark
--> pcap_filter + pcap_packet_detail: drill into suspect packets
--> AI analysis: plain-English summary of findings and recommendations
```

### NSO Device Sync and Service Audit
```
nso-device-ops + nso-service-mgmt
--> get_device_groups: list all managed device groups
--> check_device_sync for each device: identify out-of-sync devices
--> sync_from_device for out-of-sync devices: pull live config into NSO
--> get_service_types + get_services: inventory of deployed services
--> Report: "4 devices out of sync (re-synced), 12 L3VPN services healthy"
```

### CML Lab Build (Natural Language)
```
cml-lab-lifecycle + cml-topology-builder + cml-node-operations
--> "Build me a 3-router OSPF lab"
--> create_lab: new lab titled "OSPF Lab"
--> get_node_defs: verify IOSv available
--> create_node x3: R1, R2, R3 with grid layout
--> create_interface + create_link: wire R1-R2, R2-R3, R1-R3
--> set_node_config: apply OSPF startup configs
--> start_lab: boot all nodes
--> execute_command: "show ip ospf neighbor" to verify adjacencies
--> Report: "OSPF lab is ready — 3 routers, full mesh, all neighbors FULL"
```

### CML Packet Capture + Analysis
```
cml-packet-capture + packet-analysis
--> start_capture on R1-R2 link with filter "tcp port 179"
--> execute_command: "clear ip bgp *" to trigger BGP events
--> stop_capture + download_capture
--> Packet Buddy: pcap_summary, pcap_protocol_hierarchy, pcap_expert_info
--> AI analysis: "BGP OPEN/KEEPALIVE exchange completed in 2.3s, no NOTIFICATION errors"
```

### Meraki Organization Inventory
```
meraki-network-ops
--> getOrganizations: list all accessible Meraki orgs
--> getOrganizationInventory: all claimed devices (model, serial, MAC, network)
--> getOrganizationLicense: license status, expiration, device counts
--> getDeviceStatus per key device: online/offline, last seen
--> getDeviceUplink for MX appliances: WAN link health
--> Report: org-wide inventory dashboard with status and license health
```

### Meraki Wireless Health
```
meraki-wireless-ops + meraki-monitoring
--> getWirelessSSIDs: enabled SSIDs, auth types, VLANs
--> getWirelessConnectionStats: auth/DHCP success rates
--> getWirelessChannelUtilization: per-AP congestion hotspots
--> getWirelessSignalQuality: SNR trends over time
--> getWirelessRFProfiles: power and channel settings
--> Report: wireless health dashboard with per-SSID metrics
```

### Meraki Switch Port Audit
```
meraki-switch-ops + meraki-monitoring
--> getDeviceSwitchPorts: all port configs (VLAN, type, PoE, BPDU guard)
--> getDeviceSwitchPortStatuses: live speed, duplex, errors, PoE draw
--> getSwitchVlans: VLAN inventory
--> createDeviceLiveToolsCableTest: cable diagnostics on suspect ports
--> Report: port table with status, errors, and cable health
```

### Meraki MX Firewall + VPN Audit
```
meraki-security-appliance
--> getNetworkSecurityFirewallRules: L3 outbound rules
--> getNetworkVpnStatus: site-to-site VPN tunnel state
--> getNetworkSecurityVpnSiteToSite: hub/spoke config, subnets
--> getNetworkSecurityContentFiltering: blocked categories
--> getNetworkSecuritySecurityEvents: IDS/IPS detections
--> Report: MX security posture with VPN health and rule assessment
```

### SD-WAN Fabric Health Check
```
sdwan-ops
--> get_devices: verify all controllers and edges are reachable
--> get_wan_edge_inventory: check serial numbers, versions, models
--> get_alarms: identify active issues (CRITICAL, MAJOR, MINOR)
--> get_control_connections per device: verify DTLS/TLS tunnels
--> get_bfd_sessions per device: check tunnel health
--> Report: fabric status summary with severity-sorted findings
--> GAIT audit trail
```

### Grafana Infrastructure Monitoring
```
grafana-observability
--> search_dashboards(title="Network"): find network-related dashboards
--> get_dashboard_summary(uid): lightweight overview of panels
--> query_prometheus(expr="rate(ifHCInOctets{device='core-rtr-01'}[5m])*8"): interface traffic
--> query_prometheus(expr="device_cpu_utilization{device=~'.*'}"): CPU across fleet
--> list_alert_rules(folder="Network"): check alerting thresholds
--> query_loki_logs(query='{host="core-rtr-01"} |= "error"'): syslog errors
--> Report: infrastructure metrics with alert status and log correlation
--> GAIT audit trail
```

### Grafana Alert Investigation
```
grafana-observability
--> list_alert_rules: find firing or pending alert rules
--> get_alert_rule_by_uid: threshold, conditions, datasource details
--> query_prometheus: check the metric that triggered the alert
--> query_loki_logs: correlate with log events around alert time
--> list_incidents: check if already tracked
--> list_contact_points: verify notification routes
--> Report: alert analysis with root cause and metric evidence
--> GAIT audit trail
```

### Prometheus Metric Investigation
```
prometheus-monitoring
--> health_check: verify Prometheus is reachable
--> list_metrics(page=1, page_size=50): discover available metric names
--> get_metric_metadata(metric="ifHCInOctets"): check type, help, unit
--> execute_query(query="up{job='snmp'}"): check which targets are up
--> execute_range_query(query="rate(ifHCInOctets{device='core-rtr-01'}[5m])*8", start, end, step="60s"): interface traffic trend
--> get_targets: verify SNMP exporter scrape health
--> Report: metric analysis with current values and trends
--> GAIT audit trail
```

### SD-WAN Policy Audit
```
sdwan-ops
--> get_device_templates: list all templates with device counts
--> get_feature_templates: inspect VPN, interface, routing, security templates
--> get_centralized_policies: review traffic engineering and security policies
--> get_running_config per device: confirm template-applied config
--> Report: template and policy audit with recommendations
--> GAIT audit trail
```

### ThousandEyes Path Troubleshooting
```
te-network-monitoring + te-path-analysis
--> te_list_tests: find tests targeting the slow site
--> te_get_test_results: check latency, packet loss, jitter
--> te_get_path_vis: hop-by-hop path from agent to target
--> Get Full Path Visualization (official): all agents, compare paths
--> Identify hop where latency spikes or loss appears
--> Get BGP Route Details (official): is routing suboptimal?
--> Get Anomalies (official): when did degradation start?
--> Report: "Latency increase at hop 7 (ISP backbone), AS path changed at 14:32 UTC"
```

### ThousandEyes Outage Investigation
```
te-path-analysis
--> Search Outages (official): scope — ISP, CDN, SaaS provider?
--> List Events (official): which tests are affected?
--> Get Event Details (official): impacted targets, severity, timeline
--> te_get_path_vis (community): where does the path break?
--> Get BGP Test Results (official): prefix still reachable? Route withdrawn?
--> Instant Tests (official): verify from multiple cloud agents
--> Report: outage scope, affected services, root cause, estimated recovery
```

### ThousandEyes Endpoint VPN Diagnostics
```
te-path-analysis
--> List Endpoint Agents and Tests (official): find affected users
--> Get Endpoint Agent Metrics (official): WiFi signal, DNS, VPN latency
--> Get Path Visualization (official): user to VPN gateway path
--> Compare with enterprise agent test: user-side vs network-side?
--> Get Anomalies (official): when did metrics degrade?
--> Report: "WiFi -72 dBm (poor), DNS 450ms (ISP slow), VPN 180ms — switch to 5 GHz, use corporate DNS"
```

---

## Safety

NetClaw enforces non-negotiable constraints at every layer:

**Never guesses device state** — always runs a show command or live API query first.

**Never touches a device without a baseline** — pre-change state is captured and committed to GAIT before any config push.

**Never skips the Change Request** — ServiceNow CR must exist and be in `Approved` state before execution (except Emergency changes, which require immediate human notification).

**Never runs destructive commands** — `write erase`, `erase`, `reload`, `delete`, `format` are refused at the MCP server level.

**Never auto-quarantines an endpoint** — ISE endpoint group modification always requires explicit human confirmation.

**Always verifies after changes** — if post-change verification fails, the CR is not closed and the human is notified.

**Always commits to GAIT** — every session ends with `gait_log` so the human can see the full audit trail.

---

## GAIT Audit Trail

Every NetClaw session produces an immutable Git-based record of:
- What was asked
- What data was collected (and from where)
- What was analyzed (and what conclusions were reached)
- What was changed (and on what device)
- What the verification result was
- What ServiceNow tickets were created or updated

This is not optional. It is how NetClaw earns trust in production environments.

---

## Project Structure

```
netclaw/
├── SOUL.md                               # Agent personality, expertise, rules
├── AGENTS.md                             # Operating instructions, memory, safety
├── IDENTITY.md                           # Name, creature type, vibe, emoji
├── USER.md                               # Your preferences (edit this)
├── TOOLS.md                              # Local infrastructure notes (edit this)
├── HEARTBEAT.md                          # Periodic health check checklist
├── workspace/
│   └── skills/                           # 38 skill definitions (source of truth)
│       ├── meraki-network-ops/       # Meraki org inventory, networks, devices, clients
│       ├── meraki-wireless-ops/      # Meraki SSIDs, RF profiles, channel utilization
│       ├── meraki-switch-ops/        # Meraki switch ports, VLANs, ACLs, QoS
│       ├── meraki-security-appliance/ # Meraki MX firewall, VPN, content filtering
│       ├── meraki-monitoring/        # Meraki diagnostics, cameras, config change audit
│       ├── ise-posture-audit/            # ISE posture & TrustSec audit
│       ├── ise-incident-response/        # Endpoint investigation & quarantine
│       ├── servicenow-change-workflow/   # Full ITSM change lifecycle
│       ├── gait-session-tracking/        # Mandatory audit trail
│       ├── catc-inventory/              # Catalyst Center device inventory
│       ├── catc-client-ops/             # Catalyst Center client monitoring
│       ├── catc-troubleshoot/           # Catalyst Center troubleshooting
│       ├── nvd-cve/                      # NVD vulnerability search (CVSS)
│       ├── subnet-calculator/            # IPv4 + IPv6 CIDR calculator
│       ├── wikipedia-research/           # Protocol history & context
│       ├── markmap-viz/                  # Mind map visualization
│       ├── drawio-diagram/              # Draw.io network diagrams
│       ├── uml-diagram/                 # 27+ UML/diagram types via Kroki
│       ├── rfc-lookup/                   # IETF RFC search
│       ├── packet-analysis/             # pcap analysis via tshark + Slack upload
│       ├── cml-lab-lifecycle/          # CML lab create, start, stop, delete, clone
│       ├── cml-topology-builder/       # CML nodes, interfaces, links, annotations
│       ├── cml-node-operations/        # CML node start/stop, configs, CLI exec
│       ├── cml-packet-capture/         # CML link packet capture + Packet Buddy
│       ├── cml-admin/                  # CML users, groups, system, licensing
│       ├── sdwan-ops/            # Cisco SD-WAN vManage read-only monitoring (12 tools)
│       ├── grafana-observability/ # Grafana dashboards, Prometheus, Loki, alerting, incidents (75+ tools)
│       ├── prometheus-monitoring/ # Direct Prometheus PromQL queries, metric discovery, target health (6 tools)
│       ├── te-network-monitoring/   # ThousandEyes tests, agents, dashboards, alerts, events
│       ├── te-path-analysis/        # ThousandEyes path vis, BGP, outages, instant tests
│       ├── slack-network-alerts/         # Slack alert delivery
│       ├── slack-report-delivery/        # Slack report formatting
│       ├── slack-incident-workflow/      # Slack incident lifecycle
│       ├── slack-user-context/           # Slack user-aware routing
│       ├── webex-network-alerts/         # Webex alert delivery
│       ├── webex-report-delivery/        # Webex report formatting
│       ├── webex-incident-workflow/      # Webex incident lifecycle
│       └── webex-user-context/           # Webex allowlist-aware routing
├── config/
│   └── openclaw.json                     # OpenClaw model config (template)
├── mcp-servers/                          # Created by install.sh (gitignored)
│   ├── meraki-magic-mcp-community/      # Cisco Meraki Dashboard (~804 API endpoints)
│   ├── markmap_mcp/                      # Mind map visualization
│   ├── gait_mcp/                         # Git-based audit trail
│   ├── servicenow-mcp/                   # ITSM integration
│   ├── ISE_MCP/                          # Cisco ISE
│   ├── Wikipedia_MCP/                    # Technology context
│   ├── mcp-nvd/                          # NVD CVE database (Python)
│   ├── subnet-calculator-mcp/            # IPv4 + IPv6 subnet calculator
│   ├── catalyst-center-mcp/              # Cisco Catalyst Center / DNA-C
│   ├── packet-buddy-mcp/                 # pcap analysis via tshark (built-in)
│   ├── thousandeyes-mcp-community/     # ThousandEyes monitoring (9 read-only tools)
│   ├── uml-mcp/                         # 27+ diagram types via Kroki (2 tools)
│   └── cisco-sdwan-mcp/               # Cisco SD-WAN vManage monitoring (12 tools)
├── lab/
│   └── frr-testbed/                     # Docker FRR 3-router lab for protocol testing
├── scripts/
│   ├── install.sh                        # Full bootstrap installer (27 steps)
│   ├── setup.sh                          # Interactive setup wizard (API key, platforms, Slack)
│   ├── mcp-call.py                       # MCP JSON-RPC protocol handler
│   └── gait-stdio.py                     # GAIT server stdio wrapper
├── examples/
│   ├── 01_health_check.md
│   ├── 02_vulnerability_audit.md
│   ├── 03_topology_diagram.md
│   ├── 04_ospf_mindmap.md
│   ├── 05_rfc_config.md
│   └── 06_full_audit.md
├── .env.example
├── .gitignore
└── README.md
```

### What Goes Where

| Location | Purpose |
|----------|---------|
| `SOUL.md` | Agent system prompt. Defines personality, CCIE expertise, rules, and workflow orchestration |
| `AGENTS.md` | Operating instructions. Memory system, safety rules, change management, Slack behavior, escalation |
| `IDENTITY.md` | Agent identity card. Name, creature type, vibe, emoji |
| `USER.md` | About you. Preferences, timezone, role, network details. **Edit this.** |
| `TOOLS.md` | Local infrastructure. Device IPs, SSH hosts, Slack channels. **Edit this.** |
| `HEARTBEAT.md` | Periodic checks. Device reachability, OSPF/BGP state, CPU/memory, syslog. |
| `workspace/skills/` | Skill source files. `install.sh` copies these to `~/.openclaw/workspace/skills/` |
| `~/.openclaw/.env` | Meraki and other platform credentials (e.g. `MERAKI_API_KEY`, `MERAKI_ORG_ID`). No testbed file. |
| `config/openclaw.json` | Model config template. Sets primary/fallback model only — no MCP config |
| `mcp-servers/` | Tool backends cloned by `install.sh`. Gitignored — rebuilt on install |
| `scripts/mcp-call.py` | Handles MCP JSON-RPC protocol: initialize, notify, tool call, terminate |
| `scripts/gait-stdio.py` | Wraps GAIT MCP server for stdio mode (default is SSE) |

---

## What install.sh Does

1. **Checks prerequisites** — Node.js >= 18, Python 3, pip3, git, npx
2. **Installs OpenClaw** — `npm install -g openclaw@latest`
3. **Runs OpenClaw onboard** — AI provider, gateway, channels, daemon service
4. **Creates mcp-servers/** — directory for all cloned backends
5. **Clones Markmap MCP** — `git clone` + `npm install` + `npm run build`
8. **Clones GAIT MCP** — `git clone` + `pip3 install gait-ai fastmcp`
9. **Clones NetBox MCP** — `git clone` + `pip3 install` dependencies
10. **Clones Nautobot MCP** — `git clone` + `pip3 install -e .` for Nautobot IPAM source of truth (5 tools: IP addresses, prefixes, VRF/tenant/site filtering, search, connection test). Python 3.13+ required; falls back to core deps on older Python. Alternative to NetBox.
11. **Clones Infrahub MCP** — `git clone` + `pip3 install -e .` for OpsMill Infrahub schema-driven source of truth (10 tools: nodes, GraphQL queries, versioned branches). Requires Infrahub instance with API token.
12. **Clones ServiceNow MCP** — `git clone` + `pip3 install` dependencies
13. **Clones ISE MCP** — `git clone` + `pip3 install` dependencies
16. **Clones Wikipedia MCP** — `git clone` + `pip3 install` dependencies
17. **Clones NVD CVE MCP** — `git clone` + `pip3 install -e .`
18. **Clones Subnet Calculator MCP** — `git clone` (enhanced with IPv6 support)
20. **Clones Catalyst Center MCP** — `git clone` + `pip3 install` dependencies
21. **Caches Microsoft Graph MCP** — `npm cache add` for Graph API (OneDrive, SharePoint, Visio, Teams)
22. **Caches npx packages** — `npm cache add` for Draw.io and RFC servers
23. **Pulls GitHub MCP** — `docker pull ghcr.io/github/github-mcp-server` (requires Docker)
24. **Installs Packet Buddy MCP** — verifies/installs tshark, creates pcap upload directory
25. **Installs CML MCP** — `pip3 install cml-mcp` (requires Python 3.12+, CML 2.9+)
26. **Installs Meraki Magic MCP** — `git clone` + `pip install -r requirements.txt` for Cisco Meraki Dashboard API (~804 endpoints: orgs, networks, wireless, switching, security, cameras, diagnostics). Python 3.13+ recommended; falls back to core deps on older Python.
29. **Installs ThousandEyes Community MCP** — `git clone` + `pip install -r requirements.txt` for ThousandEyes monitoring (9 read-only tools: tests, agents, path vis, dashboards). Python 3.12+ required.
30. **Configures ThousandEyes Official MCP** — Remote HTTP endpoint hosted by Cisco at `https://api.thousandeyes.com/mcp` (~20 tools: alerts, outages, BGP, instant tests, endpoint agents). Pre-caches `mcp-remote` via npm. No local install required.
31. **Installs AWS Cloud MCP Servers** — Installs `uv` (Astral), validates 6 AWS MCP packages via `uvx` (Network, CloudWatch, IAM, CloudTrail, Cost Explorer, Diagram)
33. **Configures GCP Cloud MCP Servers** — Checks for `gcloud` CLI and credentials; 4 remote HTTP servers hosted by Google (Compute Engine, Cloud Monitoring, Cloud Logging, Resource Manager)
35. **Installs UML MCP** — `git clone` + `pip3 install -e .` for 27+ diagram types via Kroki multi-engine rendering (2 tools: generate_uml, generate_diagram_url). Python 3.10+ required. nwdiag (network), rackdiag (rack), packetdiag (protocol headers), sequence, state, class, C4, Mermaid, D2, Graphviz, ERD, BPMN.
36. **Installs SD-WAN MCP** — `git clone` + `pip3 install` deps (fastmcp, requests, python-dotenv) for Cisco SD-WAN vManage read-only monitoring (12 tools: fabric devices, WAN Edge inventory, templates, policies, alarms, BFD, OMP routes, control connections, running config).
38. **Installs Grafana MCP** — Validates `uvx` availability for running `mcp-grafana` (Go binary, 75+ tools: dashboards, Prometheus PromQL, Loki LogQL, alerting, incidents, OnCall, annotations, panel rendering). Requires Grafana 9.0+ with service account token.
39. **Installs Prometheus MCP** — `pip3 install prometheus-mcp-server` for direct Prometheus monitoring (6 tools: instant/range PromQL queries, metric discovery with pagination, metric metadata, scrape target health, system health check). Supports basic auth, bearer tokens, and multi-tenant org IDs.
40. **Configures Kubeshark MCP** — Checks for `kubectl`; Kubeshark MCP is a remote HTTP endpoint running inside a Kubernetes cluster (6 tools: traffic capture, pcap export, snapshots, KFL filtering, L4 flow stats, TLS decryption). Requires Kubeshark deployed via Helm with `mcp.enabled=true`.
41. **Installs Protocol MCP** — `pip3 install -r requirements.txt` (scapy, networkx, mcp, fastmcp) for live BGP/OSPF/GRE control-plane participation (10 tools: peer with routers, inject/withdraw routes, query RIB/LSDB, adjust metrics). Protocol speakers from WontYouBeMyNeighbour.
42. **Protocol Peering Wizard** — Optional interactive configuration: router ID, local AS, BGP peer IP/AS, OSPF areas, GRE tunnels, lab mode. Writes protocol environment variables to `~/.openclaw/.env`. Optionally creates GRE tunnel (requires sudo).
43. **Deploys skills + workspace files** — Copies 38 skills and 6 MD files to `~/.openclaw/workspace/`
44. **Verifies installation** — Checks all MCP server scripts + core scripts exist
45. **Prints summary** — Lists all 25 MCP servers by category and all 38 skills by domain

---

## Meraki Configuration

Device inventory is defined in Meraki Dashboard. Configure credentials in `~/.openclaw/.env`:

- **MERAKI_API_KEY** — Dashboard API key (Organization → Settings → Dashboard API access)
- **MERAKI_ORG_ID** — Organization ID (from Dashboard URL or Organization → Overview)

Run `./scripts/setup.sh` to configure Meraki and other platform credentials interactively.

---

## Prerequisites

- Node.js >= 18 (>= 22 recommended for OpenClaw)
- Python 3.x with pip3
- git
- Meraki Dashboard API key (for Meraki MCP)
- Anthropic API key

Optional (for full feature set):
- NetBox instance with API token (or Nautobot instance with API token — alternative source of truth)
- ServiceNow instance with credentials
- Cisco ISE with ERS API enabled (for ISE skills)
- NVD API key (free from https://nvd.nist.gov/developers/request-an-api-key)
- Cisco Catalyst Center (DNA Center) with API credentials
- Docker (for GitHub MCP server)
- tshark / Wireshark (for Packet Buddy pcap analysis — `apt install tshark`)
- GitHub PAT with repo scope (for GitHub MCP — https://github.com/settings/tokens)
- Cisco CML 2.9+ with API access and Python 3.12+ (for CML lab management)
- Cisco NSO with RESTCONF API enabled and Python 3.12+ (for NSO orchestration)
- Cisco Meraki Dashboard with API key and Organization ID (for Meraki wireless, switching, security, camera, and diagnostics skills — Python 3.13+ recommended)
- Cisco ThousandEyes account with API v7 OAuth bearer token and Python 3.12+ (for network monitoring, path visualization, BGP analysis, and outage investigation skills)
- AWS account with IAM credentials (`AWS_ACCESS_KEY_ID` + `AWS_SECRET_ACCESS_KEY`) for AWS cloud skills
- graphviz (`apt install graphviz` or `brew install graphviz`) for AWS architecture diagrams
- OpsMill Infrahub instance + API token (optional — schema-driven source of truth alternative)
- Google Cloud project with service account or `gcloud` CLI (for GCP Compute, Monitoring, Logging skills)
- Microsoft 365 tenant with Azure AD app registration (for Graph/Visio/Teams skills)
- Cisco SD-WAN vManage instance with API access (for SD-WAN fabric monitoring — read-only)
- Grafana 9.0+ with service account token (Editor role or granular RBAC) and `uvx` (for Grafana observability — dashboards, Prometheus, Loki, alerting, incidents, OnCall)
- Prometheus server with HTTP API access (for direct PromQL monitoring — instant/range queries, metric discovery, scrape target health)
- Kubernetes cluster with Kubeshark deployed via Helm and `kubectl` (for K8s L4/L7 traffic analysis — capture, pcap export, flow stats, TLS decryption)
- Docker for FRR lab testbed (optional — 3-router FRR topology for testing BGP/OSPF protocol participation)
- Root/sudo access for GRE tunnel creation (optional — required for BGP/OSPF peering over GRE tunnels)
- Kroki local instance (optional — public kroki.io used by default; local instance recommended for sensitive topology data)
- Slack workspace with NetClaw bot installed (for Slack skills)

---

## Example Conversations

Ask NetClaw anything you'd ask a senior network engineer:

```
"Run a health check on all devices"
--> meraki-monitoring + meraki-network-ops: device status per network, alerts, severity-sorted report

"Is our Meraki fleet vulnerable to any known CVEs?"
--> meraki-network-ops (device/firmware versions) + nvd-cve (search by product/version + CVSS scoring)

"Add a firewall rule to block high-risk countries"
--> servicenow-change-workflow (CR) + Meraki Dashboard API (action batch or update) + GAIT

"Device at the branch is offline — what's going on?"
--> meraki-monitoring (device status, uplink, events) + meraki-network-ops: diagnostics and recommended actions

"Investigate endpoint 00:11:22:33:44:55"
--> ise-incident-response: auth history, posture, profile --> human decision point

"What clients are connected to Site-A?"
--> catc-client-ops: client list filtered by site, SSID, band, health scores

"Calculate a /22 for the 10.50.0.0 network"
--> subnet-calculator: VLSM breakdown, usable hosts, wildcard mask, CIDR notation

"Show me our org and network structure as a mind map"
--> meraki-network-ops (org/networks/devices) + markmap-viz (generate mind map)

"What does RFC 4271 say about BGP hold timers?"
--> rfc-lookup: fetch RFC 4271, extract relevant section

[upload capture.pcap to Slack] "What's in this capture?"
--> packet-analysis: summary, protocol hierarchy, conversations, expert info, AI findings

"Analyze the DNS traffic in that pcap"
--> packet-analysis: pcap_dns_queries, pcap_filter (dns), plain-English analysis

"Build me a 4-router BGP lab with 2 ASes"
--> cml-lab-lifecycle + cml-topology-builder + cml-node-operations: create lab, add 4 IOSv nodes, wire topology, apply BGP configs, start lab

"Capture BGP traffic between R1 and R2 and analyze it"
--> cml-packet-capture: start capture with filter "tcp port 179", download pcap, Packet Buddy analysis

"Show me all running CML labs"
--> cml-lab-lifecycle: get_labs, list running labs with node counts and resource usage

"Export the OSPF lab topology to YAML"
--> cml-lab-lifecycle: export_lab as YAML, save for sharing or version control

"What's the CML server capacity?"
--> cml-admin: get_system_info (CPU, RAM, disk), get_licensing (node count), resource planning report

"Show me all our Meraki devices"
--> meraki-network-ops: getOrganizations, getOrganizationInventory, device status summary

"How's the WiFi at the branch office?"
--> meraki-wireless-ops: getWirelessSSIDs, getWirelessConnectionStats, getWirelessChannelUtilization, signal quality report

"What's connected to port 12 on the lobby switch?"
--> meraki-switch-ops: getDeviceSwitchPorts (port 12 config), getDeviceSwitchPortStatuses (live state), getDeviceClients

"Show me the firewall rules on the HQ MX appliance"
--> meraki-security-appliance: getNetworkSecurityFirewallRules, rule analysis for overly permissive entries

"Is the VPN tunnel to the warehouse up?"
--> meraki-security-appliance: getNetworkVpnStatus, getNetworkSecurityVpnSiteToSite, tunnel state analysis

"Run a cable test on ports 1-4 of switch MS-Floor2"
--> meraki-monitoring: createDeviceLiveToolsCableTest, getDeviceLiveToolsCableTestResults (OK/open/short/length)

"Who changed the SSID config last week?"
--> meraki-monitoring: getOrganizationConfigurationChanges filtered by time/network, admin identity and change details

"Why is traffic to example.com slow from London?"
--> te-network-monitoring: te_list_tests (find test), te_get_test_results (check latency)
--> te-path-analysis: te_get_path_vis (hop-by-hop), Get BGP Route Details (AS path check)

"Are there any network outages right now?"
--> te-path-analysis: Search Outages (official), List Events, Get Event Details, scope and timeline report

"Show me the path visualization for test 12345"
--> te-path-analysis: te_get_path_vis (community) for hop-by-hop data, Get Full Path Visualization (official) for all agents

"What BGP routes does ThousandEyes see for our prefix?"
--> te-path-analysis: Get BGP Test Results, Get BGP Route Details — AS paths, reachability from 300+ global monitors

"Run an instant test to 8.8.8.8 from our enterprise agents"
--> te-path-analysis: Instant Tests (official) — on-demand test from selected agents (consumes test units)

"Our VPN users in NYC are complaining about latency"
--> te-path-analysis: List Endpoint Agents, Get Endpoint Agent Metrics (WiFi, DNS, VPN), path visualization to gateway

"Generate a network topology diagram"
--> uml-diagram: generate_uml(type="nwdiag") with network zones, IP addressing, device placement

"Document the BGP state machine"
--> uml-diagram: generate_uml(type="state") with BGP FSM states and transitions

"Show me all SD-WAN fabric devices"
--> sdwan-ops: get_devices — vManage, vSmart, vBond, vEdge status summary

"Are there any SD-WAN alarms?"
--> sdwan-ops: get_alarms — active alarms with severity, device, and description

"Check BFD tunnel health on WAN edge 10.10.10.100"
--> sdwan-ops: get_bfd_sessions(device_ip="10.10.10.100"), tunnel status and latency report

"What OMP routes is the branch edge advertising?"
--> sdwan-ops: get_omp_routes(device_ip="10.10.10.200"), received and advertised route summary

"Show me the network dashboards in Grafana"
--> grafana-observability: search_dashboards(title="network"), get_dashboard_summary per result, panel list with datasource info

"What interface traffic is Prometheus seeing on core-rtr-01?"
--> grafana-observability: query_prometheus(expr="rate(ifHCInOctets{device='core-rtr-01'}[5m]) * 8"), query_prometheus(expr="rate(ifHCOutOctets{device='core-rtr-01'}[5m]) * 8"), utilization summary in bps

"Are any Grafana alerts firing right now?"
--> grafana-observability: list_alert_rules — firing/pending rules with severity, affected metric, threshold, and contact point summary

"Search Loki logs for BGP flaps on the spine switches"
--> grafana-observability: query_loki_logs(query='{host=~"spine.*"} |~ "BGP|Established|Idle"', limit=100), pattern analysis and timeline

"Who is on call for network incidents?"
--> grafana-observability: get_current_oncall_users, list_oncall_schedules — current responders and rotation details

"What metrics does Prometheus have for the core routers?"
--> prometheus-monitoring: health_check, list_metrics(page=1), get_metric_metadata — available metrics with type and description

"Show me the interface traffic trend on core-rtr-01 for the last hour"
--> prometheus-monitoring: execute_range_query(query="rate(ifHCInOctets{device='core-rtr-01'}[5m])*8", start, end, step="60s") — bps trend with peak and average

"Are all SNMP scrape targets up in Prometheus?"
--> prometheus-monitoring: get_targets — scrape target status (up/down), last scrape time, labels, error messages

```

See `examples/` for detailed workflow walkthroughs.

---

## Missions

| Mission | Status | Summary |
|---|---|---|
| MISSION01 | Complete | Core agent, 7 skills, Markmap, Draw.io, RFC, NVD CVE, SOUL v1 |
| MISSION02 | Complete | Meraki-focused platform — 25 MCP servers, 38 skills (Meraki, CatC, servicenow, TE, CML, Grafana, Slack, and others), SOUL v2 |
