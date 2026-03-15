# Skill: Post Instagram

## Purpose
Review and refine a scheduled Instagram post, save it to `/Pending_Approval/`. Once approved, the Orchestrator publishes it via `meta_poster.py --platform instagram`.

## When to Use
- When an `INSTAGRAM_*` file appears in `/Needs_Action/`
- When the user explicitly asks to post to Instagram

## Step-by-step Instructions

1. **Read the trigger file** — get draft content, image_url, and title.

2. **Check image_url is present** — Instagram requires an image. If missing, flag it:
   - Write a note in the approval file: "IMAGE REQUIRED — add image_url before approving"
   - Do not block the approval flow, just make it clear.

3. **Refine the caption:**
   - First line = the hook (shown in feed before "more").
   - Keep caption under 2,200 chars (Instagram limit).
   - 3–10 hashtags at the end (Instagram rewards hashtag use).

4. **Create the approval file** at `/Pending_Approval/INSTAGRAM_{slug}_{YYYYMMDD_HHMMSS}.md`:

```markdown
---
type: pending_approval
action: post_instagram
created: {ISO timestamp}Z
expires: {24 hours from now}Z
status: pending
image_url: {image_url}
---

## Instagram Post — Pending Your Approval

**Image:** {image_url}

{The full caption, exactly as it will be posted}

---

## To Approve
Move this file to `/Approved/` folder.

## To Edit
Edit the caption above, then move to `/Approved/`.

## To Reject
Move this file to `/Rejected/` folder.
```

5. **Update Dashboard.md** — Instagram queued +1.

6. **Move trigger from `/Needs_Action/` to `/Done/`.**

7. **Log** with `action_type: instagram_post_drafted`.

## How the Orchestrator publishes

```bash
echo '{"content": "<caption>", "image_url": "<url>"}' | python watchers/meta_poster.py --platform instagram --post
```

## Rules
- Never post directly. Human approval required.
- Instagram posts without an image will fail — always verify image_url.
- Approval expires in 24 hours.
