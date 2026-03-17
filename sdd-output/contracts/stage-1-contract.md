# Stage 1 Contract

**Status:** FULFILLED (commit 9d5effd)

## Exposed Guarantees

- `~/.openclaw/openclaw.json` always has `agents.defaults.systemPrompt` pointing to SOUL.md after install
- `scripts/install.sh` injects absolute SOUL.md path
- `scripts/setup.sh` re-deploys identity and re-injects systemPrompt
