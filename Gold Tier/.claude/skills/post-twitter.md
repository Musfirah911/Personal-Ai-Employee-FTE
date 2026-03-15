# Skill: Post Twitter/X

## Purpose
Review and refine a scheduled tweet, save it to `/Pending_Approval/`, and — once approved — publish it via `twitter_poster.py`. Never post directly without human approval.

## When to Use
- When a `TWITTER_*` file appears in `/Needs_Action/` (from the Twitter watcher)
- When the user explicitly asks to post a tweet

## Step-by-step Instructions

1. **Read the trigger file** in `/Needs_Action/` — get the draft content and title.

2. **Refine the tweet:**
   - Max **280 characters** (including hashtags) — hard limit, non-negotiable.
   - Count: `len(content + " " + hashtags)` before saving.
   - Keep it punchy — one clear idea per tweet.
   - Hashtags: 1–3 relevant tags at the end.

3. **Create the approval file** at `/Pending_Approval/TWITTER_{slug}_{YYYYMMDD_HHMMSS}.md`:

```markdown
---
type: pending_approval
action: post_twitter
created: {ISO timestamp}Z
expires: {24 hours from now}Z
status: pending
char_count: {count}
---

## Tweet — Pending Your Approval

{The full tweet text, exactly as it will be posted}

---

## To Approve
Move this file to `/Approved/` folder.

## To Edit
Edit the tweet text above (stay under 280 chars), then move to `/Approved/`.

## To Reject
Move this file to `/Rejected/` folder.
```

4. **Update Dashboard.md** — add to Social Queue Status table (Twitter queued +1).

5. **Move the trigger file from `/Needs_Action/` to `/Done/`.**

6. **Log the action** in today's JSONL log with `action_type: twitter_post_drafted`.

## How the Orchestrator publishes (after approval)

When the file moves to `/Approved/`, the Orchestrator runs:
```bash
echo '{"content": "<tweet text>"}' | python watchers/twitter_poster.py --post
```

## Rules

- **Never post directly.** Human approval required — no exceptions.
- **280 char hard limit.** Reject your own draft if it exceeds this. Trim and retry.
- **No fabrication.** Only reference real, verified information.
- **Approval expires in 24 hours.** Note the expiry timestamp in the approval file.
