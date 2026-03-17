# Stage 2 — Non-Cisco Removal

## 2A: Delete protocol-mcp in-repo directory

Delete `mcp-servers/protocol-mcp/` entirely (10 tools, BGP/OSPF/GRE — no value for Meraki troubleshooting).

## 2B: Delete non-Cisco skill directories (17 skills)

Delete from `workspace/skills/`:
- protocol-participation
- infrahub-sot, nautobot-sot, netbox-reconcile
- kubeshark-traffic
- aws-architecture-diagram, aws-cloud-monitoring, aws-cost-ops, aws-network-ops, aws-security-audit
- gcp-cloud-logging, gcp-cloud-monitoring, gcp-compute-ops
- msgraph-files, msgraph-teams, msgraph-visio
- github-ops

## 2C: Remove MCP install blocks from scripts/install.sh

Remove: NetBox, Nautobot, Infrahub, Microsoft Graph MCP, GitHub MCP (Docker), AWS MCPs (6 servers), GCP MCPs (4 servers), Kubeshark MCP install blocks.

Update TOTAL_STEPS and renumber log_step calls to be consistent.

## Acceptance Criteria

- `mcp-servers/protocol-mcp/` does not exist
- `workspace/skills/` reduced by 17 directories
- `install.sh` contains zero references to removed technologies
- `install.sh` step counter is consistent with no gaps
