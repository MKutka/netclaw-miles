# Example: Network Hierarchy Mind Map

## Prompt

```
Show my Meraki organization and network structure as an interactive mind map
```

## What NetClaw Does

NetClaw uses **meraki-network-ops** to get the full hierarchy (org → networks → devices), then **markmap-viz** to render it as an interactive mind map:

### Step 1: Organization & Networks

- List organizations and networks.
- Include network type (appliance, switch, wireless, combined) and device counts.

### Step 2: Devices per Network

- For each network, list devices: name, model, serial, status (online/offline).
- Optionally group by type (MX, MS, MR).

### Step 3: Generate Mind Map

NetClaw builds markdown and passes it to Markmap:

```markdown
# Organization: Your Org

## Main Office (combined)
### Appliances
#### MX67 — online
#### MX64 — online
### Switches
#### MS220-8P — online
#### MS120-8 — offline
### Wireless
#### MR42 (x2) — online

## Branch-A
### Appliances
#### MX64 — online
### Switches
#### MS120-8 — online
```

This opens as an interactive HTML mind map with zoom, collapse/expand, and pan.

## Skills Used

- **meraki-network-ops** (org, networks, devices)
- **markmap-viz** (markdown-to-mind-map rendering)
