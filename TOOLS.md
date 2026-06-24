# TOOLS.md - Local Notes

Skills define _how_ tools work. This file is for _your_ specifics — the stuff that's unique to your setup.

## What Goes Here

Things like:

- Camera names and locations
- SSH hosts and aliases
- Preferred voices for TTS
- Speaker/room names
- Device nicknames
- Anything environment-specific

## Examples

```markdown
### Cameras

- living-room → Main area, 180° wide angle
- front-door → Entrance, motion-triggered

### SSH

- home-server → 192.168.1.100, user: admin

### TTS

- Preferred voice: "Nova" (warm, slightly British)
- Default speaker: Kitchen HomePod
```

## Why Separate?

Skills are shared. Your setup is yours. Keeping them apart means you can update skills without losing your notes, and share skills without leaking your infrastructure.

## Local Tools

### SkillGuard

Security audit script for vetting external code before installation.

- **Location:** `~/jah/bin/skillguard`
- **Usage:** `skillguard <path-or-github-url>`
- **Exit codes:** `0` = PASS, `1` = WARN, `2` = BLOCK
- **Runs 10 checks:** structure, obfuscation, network calls, credential access, filesystem risk, shell execution, dependencies, permissions, repo health, size/complexity
- **Required before** installing any skill, hook, plugin, package, or external code (SOUL.md rule #10)

### Notion Content Pipeline — Tweet Approval

When n8n sends you a tweet draft for Eli's review, present it clearly and wait for Eli's response.

- **Content Pipeline DB ID (direct API / n8n / curl):** `YOUR_NOTION_CONTENT_PIPELINE_DB_ID_HERE`
- **Content Pipeline data_source_id (MCP tool calls):** `YOUR_NOTION_CONTENT_PIPELINE_DATASOURCE_ID_HERE`
- **Notion Token:** Available as `$NOTION_API_KEY` in your environment

**After Eli responds:**

If **approved** (Eli says yes/approve/ship it/looks good):
```bash
curl -s -X PATCH "https://api.notion.com/v1/pages/PAGE_ID" \\
  -H "Authorization: Bearer $NOTION_API_KEY" \\
  -H "Notion-Version: 2022-06-28" \\
  -H "Content-Type: application/json" \\
  -d '{"properties":{"Status":{"select":{"name":"Approved"}}}}'
```

If **Eli edits the text** (sends a revised version):
```bash
curl -s -X PATCH "https://api.notion.com/v1/pages/PAGE_ID" \\
  -H "Authorization: Bearer $NOTION_API_KEY" \\
  -H "Notion-Version: 2022-06-28" \\
  -H "Content-Type: application/json" \\
  -d '{"properties":{"Status":{"select":{"name":"Approved"}},"Script":{"rich_text":[{"text":{"content":"NEW_TEXT_HERE"}}]}}}'
```

If **rejected** (Eli says no/reject/skip/kill it):
```bash
curl -s -X PATCH "https://api.notion.com/v1/pages/PAGE_ID" \\
  -H "Authorization: Bearer $NOTION_API_KEY" \\
  -H "Notion-Version: 2022-06-28" \\
  -H "Content-Type: application/json" \\
  -d '{"properties":{"Status":{"select":{"name":"Rejected"}}}}'
```

Replace `PAGE_ID` with the Notion page ID provided in the review request.
The n8n Tweet Publisher workflow polls every 15 min for "Approved" items and posts them to X automatically.

**Status flow:** Idea → Drafting → Ready for Review → Approved/Rejected → Posting → Published

---

### Telegram Delivery (canonical)

All agent/system messages go to Telegram via @JAHvus_bot (display name "JAHvis").

| Target | Value |
|:---|:---|
| Owner chat (Eli) | `1021623182` |
| Bot token | Doppler `TELEGRAM_BOT_TOKEN` (project jah-openclaw, config prd) |

Prefix messages with a `[TAG]` identifying the sender/topic — e.g., `[IMPROVEMENT]`, `[DISCOVERY]`, `[SENTINEL]`, `[BUILDER]` — A "JAHvis HQ" supergroup with forum topics is planned but not yet created.

**Preferred:** OpenClaw `message` tool with `channel="telegram"`, target `1021623182`.
**Fallback curl:**
```bash
TG_TOKEN=$(doppler secrets get TELEGRAM_BOT_TOKEN --plain --project jah-openclaw --config prd)
curl -s -X POST "https://api.telegram.org/bot$TG_TOKEN/sendMessage" \\
  -d chat_id=1021623182 --data-urlencode "text=[TAG] message"
```

---

Add whatever helps you do your job. This is your cheat sheet.
