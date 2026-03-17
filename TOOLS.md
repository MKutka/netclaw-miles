# TOOLS.md — Local Infrastructure Notes

Skills define *how* tools work. This file is for *your* specifics — the environment details that are unique to your deployment.

## Network Devices

Devices are defined in `testbed/testbed.yaml`. Update that file with your SSH-accessible Cisco devices.

```
### Example Device Map
- R1 → 10.1.1.1, Core Router, IOS-XE 17.9
- R2 → 10.1.1.2, Distribution Router, IOS-XE 17.9
- SW1 → 10.1.2.1, Access Switch, IOS-XE 17.9
- SW2 → 10.1.2.2, Access Switch, IOS-XE 17.9
```

## Platform Credentials

All credentials are in `~/.openclaw/.env`. Never put credentials in skill files or this document.

```
### Connection Details (reference only — actual values in .env)
- pyATS Testbed       → PYATS_TESTBED_PATH
- NetBox              → NETBOX_URL, NETBOX_TOKEN
- ServiceNow          → SERVICENOW_INSTANCE_URL, SERVICENOW_USERNAME, SERVICENOW_PASSWORD
- Cisco APIC          → APIC_URL, APIC_USERNAME, APIC_PASSWORD
- Cisco ISE           → ISE_BASE, ISE_USERNAME, ISE_PASSWORD
- NVD API             → NVD_API_KEY
- Catalyst Center     → CCC_HOST, CCC_USER, CCC_PWD
- Microsoft Graph     → AZURE_TENANT_ID, AZURE_CLIENT_ID, AZURE_CLIENT_SECRET
```

## Slack Integration

```
### Channels
- #netclaw-alerts     → P1/P2 critical alerts
- #netclaw-reports    → Scheduled health reports, audit results
- #netclaw-general    → General queries, P3/P4 notifications
- #incidents          → Active incident threads
```

## Webex Integration

Webex is powered by the [@jimiford/webex](https://github.com/JimiHFord/openclaw-webex) OpenClaw channel plugin. Use it alongside or instead of Slack for alerts, reports, and incident workflows.

### Spaces (map to Slack channel semantics)

| Webex Space | Purpose | Alert/Report Types |
|-------------|---------|--------------------|
| NetClaw Alerts | P1/P2 critical alerts | Device down, security critical |
| NetClaw Reports | Scheduled health reports, audit results | Health checks, audits, reconciliation |
| NetClaw General | General queries, P3/P4 notifications | Ad-hoc queries, help |
| NetClaw Incidents | Active incident threads | Incident lifecycle, status, PIR |

### Configuration

- **Credentials and secrets** live in `~/.openclaw/.env`. Never put them in skills or this file.
- **Recommended DM policy:** `allowlisted` — only users in `allowFrom` (person IDs or emails) can DM the bot. Use `deny` for room-only; avoid `allow` in production.
- **Expected env vars (reference only):**
  - `WEBEX_BOT_TOKEN` — Bot access token from Webex Developer Portal
  - `WEBHOOK_URL` — Public HTTPS URL for webhooks (e.g. `https://your-domain.com/webhooks/webex`)
  - `WEBHOOK_SECRET` — Secret for webhook signature verification (e.g. `openssl rand -hex 32`)

### Operational patterns

- Keep P1/P2 to the alerts space; use threads (replies) for follow-up.
- Post concise top-level messages; put diagnostics and long output in replies.
- Reference GAIT session IDs and ServiceNow INC/CR in messages the same way as for Slack.
- See workspace skills `webex-network-alerts`, `webex-report-delivery`, `webex-incident-workflow`, and `webex-user-context` for formatting and behavior.

## Microsoft Teams Integration

```
### Teams Channels (if using Microsoft Graph for Teams delivery)
- #netclaw-alerts     → P1/P2 critical alerts, CVE exposure
- #netclaw-reports    → Health reports, audit results, reconciliation
- #netclaw-changes    → Change request updates, completion notices
- #network-general    → P3/P4 notifications, topology updates

### SharePoint Sites
- Network Engineering → Topology diagrams, audit reports, config backups
```

## SSH Access

```
### Jump Hosts / Bastion
- (your bastion host, if applicable)

### Console Servers
- (your console server, if applicable)
```

## Site Information

```
### Sites
- Site-A → Primary data center
- Site-B → DR site
- Lab    → Non-production test environment (relaxed change control)
```

## Notes

- Add whatever helps NetClaw do its job — device nicknames, maintenance windows, ISP circuit IDs, TAC case numbers, anything environment-specific.
- This file is yours. Skills are shared. Keeping them apart means you can update skills without losing your notes.
