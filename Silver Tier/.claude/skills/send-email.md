# Skill: Send Email

## Purpose
Draft and route email replies or new emails through the Human-in-the-Loop (HITL) approval workflow. This skill handles the full lifecycle: reading the context, drafting the email, creating an approval file, and logging the outcome. It never sends an email without explicit human approval.

## When to Use
- When processing an `EMAIL_*` file in `/Needs_Action/`
- When a Plan step requires sending an email to a client, vendor, or contact
- When the Orchestrator triggers you after detecting an approved email in `/Approved/`
- When a scheduled task requires sending a summary or briefing via email

## Step-by-step Instructions

1. **Read the trigger context:**
   - Read the `EMAIL_*` or plan file to understand: sender, subject, original message, and required action.
   - Read `Company_Handbook.md` for tone, sign-off style, and rules (e.g., "Always CC the project manager on client emails").

2. **Classify the email action:**

   | Action Type | Rule |
   |-------------|------|
   | Reply to known contact | Requires approval |
   | Reply to new/unknown contact | Requires approval + flag as NEW CONTACT |
   | Bulk send (>1 recipient) | Always requires approval, flag as BULK |
   | Internal email (within org) | Requires approval |
   | Any email with attachment | Requires approval + list attachment |

3. **Draft the email** following these guidelines:
   - **Subject:** Clear, specific. If replying, keep the `Re:` prefix.
   - **Greeting:** Use the sender's first name if identifiable from the From header.
   - **Body:** Professional, concise, and aligned with Company_Handbook tone.
   - **Closing:** Use the sign-off from Company_Handbook (e.g., "Best regards, [Your Name]").
   - **AI disclosure (optional):** If Company_Handbook requires it, add "Drafted with AI assistance."
   - **Keep it under 200 words** unless a detailed response is genuinely required.

4. **Create the approval file** at `/Pending_Approval/EMAIL_{slug}_{YYYYMMDD_HHMMSS}.md`:

```markdown
---
type: pending_approval
action: send_email
created: {ISO timestamp}Z
expires: {48 hours from now}Z
status: pending
to: {recipient email}
cc: {CC addresses or "none"}
subject: {email subject}
is_new_contact: {true|false}
has_attachment: {true|false}
attachment_path: {path or "none"}
---

## Email Draft — Pending Your Approval

**To:** {recipient}
**CC:** {cc or none}
**Subject:** {subject}

---

{Full email body, exactly as it will be sent}

---

## Source Context
Original message from: {sender}
Original subject: {original subject}
Received: {date}

## To Approve
Move this file to `/Approved/` to send the email via the Email MCP server.

## To Edit
Edit the email body above, then move to `/Approved/`.

## To Reject
Move this file to `/Rejected/`. No email will be sent.
```

5. **Update Dashboard.md** — add to "Upcoming Actions":
   ```
   | {timestamp} | Email pending approval | To: {recipient} | Re: {subject} |
   ```

6. **Move the original Needs_Action file to `/Done/`** (the email draft is now the active item).

7. **Log the draft** in today's JSONL log with `action_type: email_drafted`.

## After Approval (Orchestrator executes)

When the Orchestrator detects the file in `/Approved/`, it will:
1. Call the Email MCP server with the draft contents.
2. Log `action_type: email_sent` to today's log.
3. Move the approval file to `/Done/`.
4. Update Dashboard.md with "Email sent" activity.

## Rules

- **Zero auto-sends.** Every email goes through `/Pending_Approval/` first — no exceptions.
- **Flag new contacts clearly.** Set `is_new_contact: true` and add a bold warning in the approval file.
- **Never guess email addresses.** Only use addresses from the original email headers.
- **Respect rate limits.** If MAX_EMAILS_PER_HOUR is reached (from Company_Handbook), log a warning and queue the draft for next hour.
- **Approval expiry.** Email approvals expire in 48 hours. After expiry, move to /Rejected/ and notify via Dashboard.md.
- **Attachments.** Never attach files that are not explicitly listed in the source trigger. Verify the attachment path exists before creating the approval file.
