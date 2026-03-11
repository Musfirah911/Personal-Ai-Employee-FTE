# Skill: Post LinkedIn

## Purpose
Generate a compelling, authentic LinkedIn post that promotes the business, shares insights, or celebrates achievements. The post is saved to `/Pending_Approval/` — it is never published directly. The human reviews and approves it, then the Orchestrator handles publishing via the LinkedIn API.

## When to Use
- When a `LINKEDIN_*` file appears in `/Needs_Action/` (from the LinkedIn watcher)
- When a weekly briefing or business goal suggests it's time to post
- When the user explicitly asks for a LinkedIn post
- When a project milestone is completed and worth sharing publicly

## Step-by-step Instructions

1. **Read the trigger file** — understand the post topic, tone, and any specific angle requested.

2. **Gather context from the vault:**
   - Read `Business_Goals.md` → active projects, revenue milestones, KPIs
   - Scan `/Done/` for the last 7 days → completed tasks, wins, and deliverables
   - Read `Dashboard.md` → current business status
   - Read `Company_Handbook.md` → tone, brand voice, and content rules

3. **Choose the post format** based on topic and tone:

   | Tone | Format |
   |------|--------|
   | thought-leadership | Hook → 3 lessons learned → CTA |
   | achievement | Hook → What we did → Why it matters → CTA |
   | professional | Hook → Context → Key insight → CTA |
   | educational | Hook → 3-5 bullet tips → CTA |

4. **Write the post** following these LinkedIn best practices:
   - **Hook (line 1):** Bold, curiosity-driven opening. No "I'm excited to announce." Max 20 words.
   - **Body:** Short paragraphs (1-3 lines each). Use line breaks generously. Conversational.
   - **Specificity over vagueness:** "We reduced client onboarding from 5 days to 6 hours" beats "We improved our process."
   - **CTA:** End with one clear question or invitation to comment.
   - **Length:** 150–300 words for thought leadership. Up to 700 words for detailed posts.
   - **Hashtags:** 3–5 relevant hashtags at the end, on their own line.
   - **No emojis unless** tone is casual and explicitly requested.

5. **Create the approval file** at `/Pending_Approval/LINKEDIN_{slug}_{YYYYMMDD_HHMMSS}.md`:

```markdown
---
type: pending_approval
action: post_linkedin
created: {ISO timestamp}Z
expires: {24 hours from now}Z
status: pending
post_title: {title}
tone: {tone}
word_count: {count}
---

## LinkedIn Post — Pending Your Approval

**Topic:** {topic}
**Tone:** {tone}

---

{The full post text, exactly as it will be published}

---

## To Approve
Move this file to `/Approved/` folder.

## To Edit
Edit the post text above, then move to `/Approved/`.

## To Reject
Move this file to `/Rejected/` folder.
```

6. **Update Dashboard.md** — add to "Upcoming Actions":
   ```
   | {timestamp} | LinkedIn post pending approval | {post_title} |
   ```

7. **Move the Needs_Action trigger file to `/Done/`.**

8. **Log the action** in today's JSONL log with `action_type: linkedin_post_drafted`.

## Rules

- **Never publish directly.** All LinkedIn posts require human approval — no exceptions.
- **One post per trigger file.** Don't generate multiple post variants unless explicitly asked.
- **No fabrication.** Only reference real events, real numbers, and real achievements from the vault. If data isn't available, write around it.
- **Stay on brand.** Follow the tone guidelines in `Company_Handbook.md`. If no guidelines exist, default to professional and clear.
- **Respect the daily limit.** Check `Dashboard.md` — if MAX_POSTS_PER_DAY is already reached, log a warning and do not create another post draft.
- **Approval expiry.** If the approval file is not acted on within 24 hours, the Orchestrator will move it to `/Rejected/` automatically. Note this expiry in the approval file.
