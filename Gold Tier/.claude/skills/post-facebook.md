# Skill: Post Facebook

## Purpose
Review and refine a scheduled Facebook post, save it to `/Pending_Approval/`. Once approved, the Orchestrator publishes it via `meta_poster.py --platform facebook`.

## When to Use
- When a `FACEBOOK_*` file appears in `/Needs_Action/`
- When the user explicitly asks to post to Facebook

## Step-by-step Instructions

1. **Read the trigger file** — get draft content, title, and any image URL.

2. **Refine the post:**
   - Facebook allows long-form text — no hard character limit.
   - Keep it engaging: hook in the first line (visible before "See more").
   - 1–3 hashtags at the end.

3. **Create the approval file** at `/Pending_Approval/FACEBOOK_{slug}_{YYYYMMDD_HHMMSS}.md`:

```markdown
---
type: pending_approval
action: post_facebook
created: {ISO timestamp}Z
expires: {24 hours from now}Z
status: pending
---

## Facebook Post — Pending Your Approval

{The full post text, exactly as it will be published}

---

## To Approve
Move this file to `/Approved/` folder.

## To Edit
Edit the post text above, then move to `/Approved/`.

## To Reject
Move this file to `/Rejected/` folder.
```

4. **Update Dashboard.md** — Facebook queued +1.

5. **Move trigger from `/Needs_Action/` to `/Done/`.**

6. **Log** with `action_type: facebook_post_drafted`.

## How the Orchestrator publishes

```bash
echo '{"content": "<post text>"}' | python watchers/meta_poster.py --platform facebook --post
```

## Rules
- Never post directly. Human approval required.
- No fabrication — only reference real information.
- Approval expires in 24 hours.
