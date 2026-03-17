# Stage 1 — Identity Boot Fix (COMPLETE)

**Status:** Complete (commit 9d5effd)

## What Was Done

1. `systemPrompt` wired in `config/openclaw.json` → `~/.openclaw/workspace/SOUL.md`
2. `scripts/install.sh` injects absolute path after SOUL.md deploy
3. `scripts/setup.sh` re-deploys identity and re-injects `systemPrompt`

## Contract Exposed

`~/.openclaw/openclaw.json` always has `agents.defaults.systemPrompt` set after install.
