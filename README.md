# Personal AI Employee — Gold Tier

> **Local-first, agent-driven, human-in-the-loop.**
> An autonomous AI Employee powered by Claude Code + Playwright + ERPNext.

---

## What's Built Here

### Bronze Tier ✅

| Deliverable | Status |
|---|---|
| Obsidian vault with `Dashboard.md` | ✅ |
| `Company_Handbook.md` (rules of engagement) | ✅ |
| Folder structure: `/Inbox`, `/Needs_Action`, `/Done` | ✅ |
| Filesystem Watcher script | ✅ |
| Claude Code reading/writing vault | ✅ |
| Agent Skills (`/process-inbox`, `/update-dashboard`, `/triage-task`) | ✅ |

### Silver Tier ✅

| Deliverable | Status |
|---|---|
| Gmail Watcher (polls every 2 min, creates action items) | ✅ |
| LinkedIn Scheduler (reads queue, triggers posts) | ✅ |
| Cron Scheduler (daily/weekly briefing triggers) | ✅ |
| Upgraded Orchestrator (MCP execution, approval expiry) | ✅ |
| New Agent Skills (`/create-plan`, `/post-linkedin`, `/send-email`, `/weekly-briefing`) | ✅ |
| MCP config (`mcp.json`) for email + browser servers | ✅ |
| `.env.example` for all secrets and schedule times | ✅ |
| `Approved/` folder — orchestrator watches and executes | ✅ |
| `Plans/LinkedIn_Queue.md` — scheduled post queue | ✅ |

### Gold Tier ✅

| Deliverable | Status |
|---|---|
| Twitter/X Playwright poster (cookie-based, no API key needed) | ✅ |
| Facebook Playwright poster (persistent profile, auto-login) | ✅ |
| Instagram Playwright poster (image upload + caption) | ✅ |
| ERPNext accounting watcher (polls open invoices every 15 min) | ✅ |
| ERPNext MCP server (full CRUD on doctypes) | ✅ |
| Ralph Wiggum loop (stop hook — keeps Claude working until TASK_COMPLETE) | ✅ |
| Gold orchestrator (Twitter, Facebook, Instagram execution) | ✅ |
| `Social_Queue/` — Twitter, Facebook, Instagram queue files | ✅ |
| `start.bat` — one-click launch of all watchers | ✅ |
| Full HITL pipeline — every post goes through `/Pending_Approval/` first | ✅ |

---

## How It Works

```
┌─────────────────────────────────────────────────────┐
│                    WATCHERS                         │
│  gmail_watcher    → polls Gmail every 2 min         │
│  twitter_watcher  → reads Social_Queue/Twitter_Queue │
│  facebook_watcher → reads Social_Queue/Facebook+IG  │
│  erpnext_watcher  → polls ERPNext every 15 min      │
│  scheduler        → fires daily/weekly briefings    │
└──────────────────────┬──────────────────────────────┘
                       │ writes .md trigger files
                       ▼
            AI_Employee_Vault/Needs_Action/
                       │
                       ▼
              Orchestrator → Claude Code
                       │
                       ▼
            /Pending_Approval/  ← human reviews
                       │
                 move to /Approved/
                       │
                       ▼
              Orchestrator executes:
              • twitter_poster.py   → X.com
              • meta_poster.py --fb → Facebook
              • meta_poster.py --ig → Instagram
              • linkedin_poster.py  → LinkedIn
              • email-mcp           → Gmail send
```

**Nothing executes without human approval.**

---

## Quick Start

### 1. Prerequisites

- Python 3.13+
- Node.js 18+
- Claude Code: `npm install -g @anthropic/claude-code`
- Playwright: `pip install playwright && python -m playwright install chromium`

### 2. Install dependencies

```bash
cd "Gold Tier"
pip install -r requirements.txt
cd ERP_Next-MCP && uv sync && cd ..
```

### 3. Configure environment

```bash
cp .env.example .env
# Fill in your credentials — see .env.example for all options
# Set DRY_RUN=false when ready to go live
```

### 4. Log in to social platforms (one-time setup)

```bash
# Twitter/X — opens browser, log in manually, saves cookies
python watchers/twitter_poster.py --setup

# Facebook + Instagram — uses persistent Chrome profile
python watchers/meta_poster.py --platform facebook --test-login
python watchers/meta_poster.py --platform instagram --test-login

# LinkedIn
python watchers/linkedin_poster.py --setup
```

### 5. Start everything

```bash
start.bat
```

Or start individually:
```bash
python watchers/orchestrator.py
python watchers/twitter_watcher.py
python watchers/facebook_watcher.py
python watchers/gmail_watcher.py
python watchers/scheduler.py
```

### 6. Use Claude with MCP servers

```bash
claude --mcp-config mcp.json
```

---

## Vault Structure

```
AI_Employee_Vault/
├── Dashboard.md           ← Real-time status (auto-updated)
├── Company_Handbook.md    ← Rules of engagement for the AI
├── Business_Goals.md      ← Your Q1 targets and KPIs
├── Inbox/                 ← Drop files here
├── Needs_Action/          ← Watcher-created task triggers
├── Pending_Approval/      ← Awaiting your sign-off
├── Approved/              ← Approved → orchestrator executes
├── Rejected/              ← Denied or expired (>48h)
├── Done/                  ← Completed archive
├── Plans/                 ← Multi-step plans
├── Briefings/             ← Weekly CEO reports
├── Social_Queue/          ← Twitter / Facebook / Instagram queues
├── Accounting/            ← ERPNext snapshots
├── Audit_Reports/         ← Weekly business audit outputs
└── Logs/                  ← JSONL audit trail (one file per day)
```

---

## Agent Skills

Located in `.claude/skills/`:

| Skill | Invocation | Purpose |
|---|---|---|
| Process Inbox | `/process-inbox` | Triage all pending tasks |
| Update Dashboard | `/update-dashboard` | Refresh Dashboard.md |
| Triage Task | `/triage-task <file>` | Deep-analyse one task |
| Create Plan | `/create-plan` | Build a multi-step plan |
| Post LinkedIn | `/post-linkedin` | Draft LinkedIn post |
| Post Twitter | `/post-twitter` | Draft tweet (max 280 chars) |
| Post Facebook | `/post-facebook` | Draft Facebook post |
| Post Instagram | `/post-instagram` | Draft Instagram post + image |
| Send Email | `/send-email` | Draft email (requires approval) |
| Accounting Audit | `/accounting-audit` | Pull ERPNext snapshot |
| Weekly Briefing | `/weekly-briefing` | Generate CEO weekly summary |

---

## Security

- `DRY_RUN=true` by default — no real actions until you flip it
- `.env` never committed (`.gitignore` enforced)
- All external actions require human approval (HITL)
- 48-hour expiry on approvals — stale actions auto-rejected
- Full JSONL audit trail in `/Logs/`

---

## Tier Roadmap

| Tier | Status | Key Additions |
|---|---|---|
| **Bronze** | ✅ Done | Vault, Filesystem Watcher, Agent Skills |
| **Silver** | ✅ Done | Gmail, LinkedIn, Cron Scheduler, MCP Email, Orchestrator |
| **Gold** | ✅ Done | Twitter, Facebook, Instagram, ERPNext, Ralph Wiggum loop |
| **Platinum** | 🔜 | Cloud VM, always-on, Local+Cloud hybrid |
