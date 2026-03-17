# Stage 2 Contract

## Exposed Guarantees

- `mcp-servers/protocol-mcp/` does not exist in the repo
- `workspace/skills/` has exactly 54 directories (71 starting - 17 removed)
- `install.sh` contains zero references to: netbox, nautobot, infrahub, msgraph, kubeshark, awslabs, cloudwatch-mcp, iam-mcp, cloudtrail-mcp, cost-explorer-mcp, github-mcp-server (Docker)
- `install.sh` step counter is consistent with no gaps

## Downstream Requirements

Stage 3 documentation must reflect the post-removal skill count (54) and MCP count from this stage.
