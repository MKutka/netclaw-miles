# Example: Network Topology Diagram

## Prompt

```
Discover my Meraki network structure and draw a diagram
```

## What NetClaw Does

NetClaw uses **meraki-network-ops** to get org → networks → devices and uplink relationships, then **drawio-diagram** to generate the diagram:

### Step 1: Organization & Networks

- List organizations and networks via Meraki MCP.
- Get network names, types (appliance, switch, wireless), and tags.

### Step 2: Device Hierarchy

- List devices per network: MX, MS, MR, MV, sensors.
- Get device names, models, serials, and uplink info (e.g. which switch port or upstream device).

### Step 3: Build Topology Model

NetClaw assembles a structure:

```
Org: Your Org
  Network: Main Office (combined)
    MX67 — WAN1 → ISP, LAN → MS220-8P
    MS220-8P — uplink from MX67, ports to MR42, MR42, clients
    MR42 (x2) — uplink from MS220-8P
  Network: Branch-A
    MX64 — WAN1 → ISP
    MS120-8 — uplink from MX64
```

### Step 4: Generate Draw.io Diagram

- Use **drawio-diagram** (Mermaid or direct nodes/edges) to render org → networks → devices and links.
- Diagram can show uplink relationships and device roles.

## Skills Used

- **meraki-network-ops** (org, networks, devices, uplinks)
- **drawio-diagram** (Mermaid-to-Draw.io or structured diagram)
