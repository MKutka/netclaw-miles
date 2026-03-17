---
name: webex-user-context
description: "Leverage Webex space membership, allowlist policy, and conversation context to personalize responses, route escalations, and coordinate team operations when using the Webex channel"
user-invocable: true
metadata:
  { "openclaw": { "requires": { "bins": ["python3"] } } }
---

# Webex User Context

Use Webex channel awareness to make NetClaw's interactions smarter when operating via Webex — respecting the allowlist policy, routing escalations appropriately, and personalizing response depth based on who is in the space or who sent the message.

## Webex DM Policy (Allowlist)

The Webex channel is configured with **dmPolicy: allowlisted** by default. Only users explicitly listed in `allowFrom` (person IDs or emails) can send direct messages to the bot and receive responses. This is the recommended production setting.

- **allowlisted:** Only users in `allowFrom` get DM responses. Use for production.
- **deny:** No DMs; only space (room) messages are handled.
- **allow:** Anyone can DM the bot — avoid in production.

When a non-allowlisted user DMs the bot, they do not receive a response. When posting to a space, anyone in the space can see NetClaw's replies in that space.

## User-Aware Behaviors

### 1. Escalation Routing

When escalating an alert or incident in Webex:

1. **Check allowlist** — Is the target user in the Webex channel's `allowFrom` list? If not, they cannot be DM'd; use space mentions instead so they see the message in the space.
2. **Prefer space for P1/P2** — Post to the incident or alerts space and @mention the on-call or IC so the whole team sees the escalation.
3. **DM only for allowlisted users** — If sending a direct message, ensure the recipient is allowlisted; otherwise post in the relevant space and mention them.

**Escalation in Webex:**

| Severity | Action in Webex |
|----------|-----------------|
| P1/P2 | Post to NetClaw Incidents (or Alerts) and @mention IC / on-call |
| P3/P4 | Post to NetClaw Alerts or General; optional @mention |
| Allowlisted user | Can receive DM in addition to space posts |

### 2. Personalized Response Depth

Adjust response complexity based on who is asking (when identity is known from the message author):

| Role / context | Response Style |
|----------------|----------------|
| Network Engineer, CCIE | Full technical detail, CLI output, protocol specifics |
| NOC, Operations | Actionable steps, severity, impact summary |
| Manager, Director | Executive summary, business impact, timeline |
| Security, InfoSec | Focus on security implications, CVEs, compliance |
| Developer, DevOps | API perspectives, connectivity impact, service mapping |
| Help Desk, Support | Simple status, ETA, who to contact |

In Webex, use the message author's display name or email (when available in the envelope) to infer role if documented in TOOLS.md or USER.md.

### 3. Space-Aware Posting

- **NetClaw Alerts** — High-signal only. Avoid clutter; use reply threads for detail.
- **NetClaw Reports** — Scheduled and ad-hoc reports; long content in reply threads or file attachments.
- **NetClaw Incidents** — One thread per incident; all updates in that thread.
- **NetClaw General** — General queries, P3/P4, help. More conversational.

Post progress updates during multi-step tasks (per AGENTS.md): after each major milestone, post a brief status in the same thread so the team sees progress without being pinged repeatedly.

### 4. Context from Previous Messages

When a user asks a follow-up in the same space or thread:

- Reference the current thread for context (incident, report, or alert under discussion).
- Don't re-explain concepts already covered in the thread.
- Build on previous analysis rather than starting fresh.

## Team Awareness

### On-Call and IC Assignment

When NetClaw needs to escalate:

```
📞 **Escalation**
Posting to incident space and notifying team.

• @engineer1 — SELECTED (Network Engineer)
• @engineer2 — Next in rotation (Senior NetEng)
• @manager1 — CC'd on P1

Escalating to @engineer1...
```

Use @mentions in the space so the right people get notified. Document on-call rotation or IC preferences in TOOLS.md if needed.

### Handoff Between Shifts

When a shift change is approaching, post a handoff summary in the reports or general space:

```
🔄 **Shift Handoff Summary for @nightshift_engineer**

**Active Items:**
1. ⚠️ R2 memory trending high (78% → 82% over 8h) — monitoring
2. 🔧 SW1 firmware upgrade scheduled for 02:00 UTC — prep done
3. ✅ R1 Loopback99 change completed and verified

**Pending:**
4. ⏳ Waiting on ISP ticket #45678 for R3 circuit
5. 📋 Security audit scheduled for SW2 tomorrow

_Full context in NetClaw Reports thread →_
```

## Privacy and Boundaries

- **Never expose** who is or isn't on the allowlist to other users.
- **Only use** identity to improve routing and response quality.
- **Don't track** user activity beyond immediate operational context.
- **Be transparent** when personalizing (e.g., "Given your role, here's a concise summary...").

## Integration with Other Skills

| Skill | User Context Enhancement |
|-------|--------------------------|
| webex-network-alerts | Route to correct space; @mention for escalation |
| webex-incident-workflow | IC assignment; post updates in incident thread |
| webex-report-delivery | Adjust detail level per audience; use reply threads for depth |
| All pyATS skills | Include requestor context in GAIT trail when known |
