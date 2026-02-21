# Personal AI Employee — Bronze Tier

> **Local-first, agent-driven, human-in-the-loop.**
> An autonomous AI Employee powered by Claude Code and Obsidian.

---

## What's Built Here (Bronze Tier)

| Deliverable | Status |
|---|---|
| Obsidian vault with `Dashboard.md` | ✅ |
| `Company_Handbook.md` (rules of engagement) | ✅ |
| Folder structure: `/Inbox`, `/Needs_Action`, `/Done` | ✅ |
| Filesystem Watcher script | ✅ |
| Claude Code reading/writing vault | ✅ |
| Agent Skills (`/process-inbox`, `/update-dashboard`, `/triage-task`) | ✅ |

---

## Quick Start

### 1. Prerequisites

- Python 3.13+
- Claude Code installed (`npm install -g @anthropic/claude-code`)
- Obsidian (open `AI_Employee_Vault/` as a vault)

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Configure environment

```bash
cp .env.example .env
# Edit .env — set DRY_RUN=true during development
```

### 4. Open the vault in Obsidian

Open Obsidian → "Open folder as vault" → select `AI_Employee_Vault/`

### 5. Start the Filesystem Watcher

```bash
python watchers/filesystem_watcher.py
```

Drop any file into `AI_Employee_Vault/Inbox/` — the watcher will automatically create an action item in `/Needs_Action/`.

### 6. Start the Orchestrator (optional, triggers Claude automatically)

```bash
python watchers/orchestrator.py --dry-run
```

Remove `--dry-run` when you're ready for Claude to act.

### 7. Use Claude Code Agent Skills

From your terminal, with Claude Code pointed at this project:

```bash
# Process all pending items in /Needs_Action
/process-inbox

# Refresh the Dashboard
/update-dashboard

# Deeply analyse a specific task
/triage-task FILENAME.md
```

---

## Vault Structure

```
AI_Employee_Vault/
├── Dashboard.md           ← Real-time status summary
├── Company_Handbook.md    ← Rules of engagement for the AI
├── Business_Goals.md      ← Your Q1 targets and KPIs
├── Inbox/                 ← Drop files here
├── Needs_Action/          ← Watcher creates .md tasks here
├── Done/                  ← Completed tasks
├── Plans/                 ← Multi-step plans created by Claude
├── Pending_Approval/      ← Awaiting your sign-off
├── Approved/              ← You approved → orchestrator acts
├── Rejected/              ← You rejected
├── Briefings/             ← CEO briefings (Gold tier)
└── Logs/                  ← JSONL audit logs
```

---

## Agent Skills

Located in `.claude/skills/`:

| Skill | Invocation | Purpose |
|---|---|---|
| Process Inbox | `/process-inbox` | Triage all pending tasks |
| Update Dashboard | `/update-dashboard` | Refresh Dashboard.md |
| Triage Task | `/triage-task <file>` | Deep-analyse one task |

---

## Architecture

```
[You drop a file into /Inbox]
        ↓
[FilesystemWatcher detects it]
        ↓
[Creates .md in /Needs_Action]
        ↓
[Orchestrator triggers Claude Code]
        ↓
[Claude reads Company_Handbook.md]
        ↓
  ┌─────────────────────┐
  │ Autonomous task?    │──Yes──→ [Complete & move to /Done]
  └─────────────────────┘
          │ No
          ↓
  [Create /Pending_Approval file]
          ↓
  [You review & move to /Approved]
          ↓
  [Orchestrator executes action]
          ↓
  [Log to /Logs/YYYY-MM-DD.jsonl]
```

---

## Security

- `DRY_RUN=true` by default — no real external actions until you flip it
- Credentials live in `.env` only (never committed)
- All actions logged to `/Logs/` as JSONL for full audit trail
- Payments and emails always require human approval (HITL)

---

## Tier Roadmap

| Tier | Status | Key Additions |
|---|---|---|
| **Bronze** | ✅ Built | Vault, Watcher, Skills |
| **Silver** | 🔜 | Gmail Watcher, LinkedIn posting, MCP email server |
| **Gold** | 🔜 | Full cross-domain, Odoo, CEO Briefing, Ralph Wiggum loop |
| **Platinum** | 🔜 | Cloud VM, always-on, Local+Cloud split |

---

## Submission

- **Tier:** Bronze
- **Tech:** Python, watchdog, Claude Code, Obsidian
- **Submit:** https://forms.gle/JR9T1SJq5rmQyGkGA
