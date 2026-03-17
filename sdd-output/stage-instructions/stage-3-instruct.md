# Stage 3 — Documentation Update

## 3A: Update SOUL.md

1. Update skill count from "77" to "54" and MCP server count from "36" to "28"
2. Remove domain sections: Source of Truth (netbox/nautobot/infrahub entries), Protocol Participation, Cloud AWS, Cloud GCP, Microsoft 365, GitHub, Kubernetes (kubeshark)
3. Remove workflow sections: SoT reconciliation, Infrahub operations, Protocol Participation, Kubeshark, Microsoft 365
4. Remove Rule #6 (NetBox read-write)
5. Remove NetBox cross-reference mentions in workflow sections

## 3B: Update README.md

1. Update skill counts to 54
2. Update MCP server count
3. Remove capability sections for removed technologies

## 3C: Update .env.example

Remove env var sections for: GitHub MCP, NetBox, Nautobot, Infrahub, AWS, GCP, Kubeshark, Protocol Participation.
