# Example: Device Health Check

## Prompt

```
Run a health check on my Meraki network (or a specific device)
```

## What NetClaw Does

NetClaw uses **meraki-monitoring** and **meraki-network-ops** to assess device and network health:

### Step 1: Device Status

- List organizations and networks via Meraki MCP.
- Get device status: online/offline, last seen, uplink status, LAN/WAN addresses.
- Identify any devices with alerts (offline, uplink down, high latency).

### Step 2: Utilization & Performance

- Use Meraki Dashboard API or MCP for device performance data where available (e.g. appliance Uplink status, switch port status).
- For wireless: client counts, RF metrics, connectivity issues.

### Step 3: Connectivity

- Live ping from device (Meraki MCP) when supported.
- Uplink health and latency from Dashboard.

### Step 4: Summarize

NetClaw produces a health summary:

- Devices online/offline per network.
- Any reported alerts or events.
- Recommended actions (e.g. investigate offline device, check uplink).

## Example Output

```
Organization: Your Org
Networks: 3

┌──────────────────┬──────────┬─────────────────────────┐
│ Network          │ Status   │ Details                 │
├──────────────────┼──────────┼─────────────────────────┤
│ Main Office      │ HEALTHY  │ 12/12 devices online     │
│ Branch-A         │ WARNING  │ 1 device offline (SW-2) │
│ Guest-WiFi       │ HEALTHY  │ 5/5 APs online           │
└──────────────────┴──────────┴─────────────────────────┘

Alerts: 1 — SW-2 (Branch-A) offline since 14:30 UTC. Check power/uplink.
```

## Skills Used

- **meraki-monitoring** (device status, alerts)
- **meraki-network-ops** (org/network/device hierarchy, live tools where available)
