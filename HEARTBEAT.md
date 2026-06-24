# HEARTBEAT.md

## Quick Checks (rotate — pick 1-2 per heartbeat)

- [ ] `docker ps` — are n8n, postgres, redis, langfuse all running?
- [ ] OpenClaw gateway health — any errors in recent logs?
- [ ] `openclaw status` — check session health, token usage, model status
- [ ] Disk usage — still under 50%?
- [ ] Check memory/ folder — is today's daily log started?
- [ ] Curate MEMORY.md — review recent daily logs, distill key facts (every few days)

## Rules

- If everything is fine: reply HEARTBEAT_OK (one line, save tokens)
- If something needs attention: report it concisely
- Do NOT repeat checks you ran less than 2 hours ago
- Quiet hours: 23:00–08:00 ET — always HEARTBEAT_OK unless critical

## Context Pruning (run every 24h or every 50 interactions)

- [ ] Archive memory/ files older than 7 days to memory/archive/
- [ ] Review Supermemory containers — forget entries older than 30 days that are no longer relevant
- [ ] Compress multi-line memory entries to single lines
- [ ] Remove completed tasks, resolved decisions, outdated context from daily logs
- [ ] Log: "[DATE] [MAINTENANCE] Context pruned. ~X old entries archived."

---

## Cron-Specific Protocols

**The Nightly Advisory Board and Morning Briefing protocols have been moved to `ops/cron-protocols.md`.** Only JAHvus cron sessions should read that file. If you are a cron session for the Advisory Board or Morning Briefing, read `ops/cron-protocols.md` now.
