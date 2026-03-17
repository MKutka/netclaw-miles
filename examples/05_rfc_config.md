# Example: Standards-Informed Meraki Configuration

## Prompt

```
Add a firewall rule to block high-risk countries, following best practices
```

## What NetClaw Does

NetClaw uses **rfc-lookup** (or security best practices) for guidance, **servicenow-change-workflow** for the CR, and Meraki Dashboard API / MCP for the change:

### Step 1: Research Best Practices

- Use **rfc-lookup** or **meraki-security-appliance** for guidance on firewall design (e.g. default-deny, logging, ordering).
- Optionally reference NVD/CVE for known risks.

### Step 2: Pre-Change Baseline

- Get current L3 firewall rules (Meraki MCP or Dashboard API).
- Record rule order and any existing geo-blocking.
- Create ServiceNow CR and get approval.

### Step 3: Build and Apply Change

- Compose new rule(s) (e.g. deny traffic from specific country codes, with logging).
- Apply via Meraki Dashboard API (action batch or single update).
- Use **servicenow-change-workflow** to tie the change to the CR.

### Step 4: Post-Change Verification

- Re-fetch firewall rules and confirm the new rule is present and in the right order.
- Check Meraki event log for the change.
- Close CR on success.

### Step 5: Document

- Change report: rule added, CR reference, verification result.
- GAIT record of the session.

## Skills Used

- **rfc-lookup** (optional — security/firewall standards)
- **meraki-security-appliance** (firewall rules, best practices)
- **servicenow-change-workflow** (CR, approval, audit)
- Meraki MCP / Dashboard API (read/write rules)
