# Webex Integration Verification Checklist

Use this checklist to verify that the Webex channel is correctly installed and that Netclaw-Miles can send and receive messages via Webex.

## Prerequisites

- [ ] Node.js and OpenClaw installed; Netclaw-Miles workspace deployed (e.g. `./scripts/install.sh` and `openclaw onboard` completed).
- [ ] A public HTTPS URL available for webhooks (production) or a tunnel such as ngrok for development.

## 1. Webex Bot

- [ ] **Create a Webex bot** at [developer.webex.com](https://developer.webex.com/): My Webex Apps → Create a New App → Create a Bot.
- [ ] **Bot name and username** set (e.g. `NetClaw` / `netclaw@webex.bot`).
- [ ] **Bot Access Token** copied and stored securely (e.g. in `~/.openclaw/.env` as `WEBEX_BOT_TOKEN`). You only see it once in the portal.

## 2. OpenClaw Webex Plugin

- [ ] **Plugin installed** where OpenClaw can load it: `npm install @jimiford/webex` (in the project or global Node environment used by the gateway).
- [ ] **OpenClaw config** includes:
  - `channels.webex.enabled: true`
  - `channels.webex.token` (or from env)
  - `channels.webex.webhookUrl` (your public webhook URL)
  - `channels.webex.webhookSecret` (e.g. from `openssl rand -hex 32`)
  - `channels.webex.dmPolicy: "allowlisted"`
  - `channels.webex.allowFrom` with at least one person ID or email for testing
  - `plugins.load.paths` including the path to `@jimiford/webex` (e.g. `node_modules/@jimiford/webex`).
- [ ] **Secrets in env** — `WEBEX_BOT_TOKEN`, `WEBHOOK_URL`, `WEBHOOK_SECRET` in `~/.openclaw/.env` (or equivalent); never committed.

## 3. Webhooks

- [ ] **Gateway running** — `openclaw gateway` (or your daemon) is running so the webhook endpoint is reachable.
- [ ] **Webhooks registered with Webex** — Either the plugin registers them on startup or you have run the registration step from the [@jimiford/webex](https://github.com/JimiHFord/openclaw-webex) docs.
- [ ] **Webhook URL** matches exactly what is configured in OpenClaw and in Webex (including path, e.g. `/webhooks/webex`).
- [ ] **HTTPS** — Webex requires HTTPS for webhook delivery.

## 4. Test Alert Path

- [ ] **Bot added to a space** — Create or use a Webex space and add the bot so it can post there.
- [ ] **Send a test message to the bot** in that space (e.g. "Run a health check on R1" or "Hello").
- [ ] **Confirm the bot receives it** — Check gateway/logs for incoming webhook and agent handling.
- [ ] **Confirm the bot replies** in the same space (or thread). If replies do not appear, verify: room ID vs person ID handling (see [openclaw-webex](https://github.com/JimiHFord/openclaw-webex) troubleshooting), webhook signature, and that the agent is replying to the correct conversation ID.
- [ ] **DM test (optional)** — If your user is in `allowFrom`, send a DM to the bot and confirm you get a response. Users not in `allowFrom` should not receive DM replies.

## 5. Netclaw-Miles Skills

- [ ] **Webex skills deployed** — `webex-network-alerts`, `webex-report-delivery`, `webex-incident-workflow`, `webex-user-context` are present in `~/.openclaw/workspace/skills/` (or your workspace path).
- [ ] **TOOLS.md** — Webex spaces (NetClaw Alerts, NetClaw Reports, etc.) and env vars are documented for your environment.

## Troubleshooting

- **No webhook received:** Check firewall, tunnel, and URL; ensure Webex can reach the gateway.
- **Invalid webhook signature:** Ensure `webhookSecret` matches the secret used when registering the webhook; header is `x-spark-signature`.
- **Replies not reaching Webex:** Verify room ID vs person ID (base64-encoded IDs; room IDs decode to a path containing `/ROOM/`). See the [Medium article](https://medium.com/@jimifordmail/building-a-webex-channel-plugin-for-openclaw-an-ai-assisted-development-story-ec2ccd31f984) and plugin docs.
- **DM not working:** Confirm `dmPolicy` is `allowlisted` and your user (person ID or email) is in `allowFrom`.

When all items are checked, the Webex channel is ready for use alongside Slack with the same alert, report, and incident workflows.
