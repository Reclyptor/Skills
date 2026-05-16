---
description: Guides investigation of failed or disputed notification deliveries, including cron-triggered runs. Use when a user reports a scheduled notification was not received, or when a send_notification call returned success but delivery is in doubt.
allowed-tools: sessions_history send_notification
metadata:
  origin: agent
---

# Notification Delivery Troubleshooting

## When to Use
- User reports a scheduled or cron-triggered notification was not received.
- `send_notification` returned `{"sent": true}` but the user disputes delivery.
- A cron run appears to have completed but no visible action occurred.

---

## Critical Pitfall: API Success ≠ Actual Delivery

`send_notification` (and similar delivery APIs) returning `{"sent": true}` only confirms the **API accepted the request**. It does **not** confirm:
- The notification was rendered in the user's UI.
- The push/delivery backend forwarded it.
- The user's device or client received it.

**Never contradict a user's report of non-delivery by citing an API success code alone.** If the user says it wasn't received, assume delivery failed and investigate further.

---

## Critical Pitfall: Natural-Language Cron Commands Don't Reliably Trigger Tool Calls

When a cron job's command is written in natural language (e.g., *"send a notification to the user"*), the spawned agent may not actually call `send_notification`. Natural language is interpreted, not executed — the spawned agent can satisfy the prompt by logging, responding with text, or doing nothing visible.

**Symptom:** The cron run record shows execution completed, but no `send_notification` call appears in the session's tool call log.

### Fix: Write Explicit Tool-Call Instructions

Rewrite the cron command to name the tool and its arguments directly:

> ❌ Bad: `"Send a push notification reminding the user about their 3pm meeting"`
>
> ✅ Good: `"Call send_notification with title='Reminder' and body='Your 3pm meeting starts soon'"`

Be as explicit as possible. Include all required fields by name. Even with this approach, tool-call compliance from spawned agents cannot be fully guaranteed from the command alone — but explicit instructions significantly improve reliability.

### Updating a Cron Job

The cron tool does **not** support in-place updates. To change a cron command:
1. **Delete** the existing job by ID.
2. **Recreate** it with the corrected command and the same schedule.

#### Timezone Pitfall When Updating a Trigger Time

Cron schedules are expressed in **UTC by default**. Users almost always state times in their **local timezone**. These will silently diverge.

**When a user asks to change a trigger time (e.g., "set it to 9:05 AM"):**
1. Clarify or confirm the intended timezone before applying the change.
2. If UTC is assumed, state the UTC value explicitly *and* its local-time equivalent so the user can verify.
3. Offer to offset the cron expression to match the user's local timezone if UTC is not what they intended.

> ✅ Good response: "Updated to `9:05 AM UTC` — that fires at 5:05 AM Eastern. Is UTC correct, or should I adjust the cron to match your local time?"

Do **not** silently apply a UTC cron expression for a time the user stated without local context, as this is a common and hard-to-debug source of missed or mistimed notifications.

---

## Debugging Workflow

### Step 1 — Confirm the cron run actually fired
- List recent run records for the relevant cron job or agent.
- Verify a run entry exists at the expected time.

### Step 2 — Locate the session for that run
- Extract the session ID from the run record.
- Attempt to look it up via `sessions_history`.

> **Known gap:** Sessions created by cron-triggered runs are often **not accessible via `sessions_history`**. Their threads may be cleaned up after completion, or routing differs from user-initiated sessions. A missing entry does not mean the session didn't execute.

### Step 3 — Check session status directly
- Query session status by ID through a direct endpoint (not history listing).
- Look for the tool call sequence and any `send_notification` invocation.

### Step 4 — Evaluate the result in context
- If `send_notification` shows `{"sent": true}`: note this as "API accepted," not "delivered."
- Cross-reference with:
  - Frontend/UI delivery logs if available.
  - Push notification backend status.
  - Whether any frontend error or silent failure was recorded around the same timestamp.

### Step 5 — Communicate honestly
- Report what the logs show, including the limitation of API success codes.
- Offer to investigate the delivery pipeline (frontend, push backend, device) separately.
- Do **not** close the investigation by asserting delivery was successful if the user says otherwise.

---

## Common Root Causes

| Symptom | Likely Cause |
|---|---|
| `send_notification` success but nothing received | Push backend failure, device token stale, silent UI error |
| Session ID not found in `sessions_history` | Cron sessions aren't indexed in history — check direct status |
| Run record exists but no tool calls visible | Session thread was cleaned up; check direct session endpoint |
| Notification received but delayed | Push delivery batching or background app refresh |
| Cron ran but `send_notification` was never called | Cron command was natural language — rewrite with explicit tool-call instructions |
| Notification fires at wrong time | Cron schedule set in UTC but user intended local time — always confirm timezone |

---

## Metadata
- origin: agent