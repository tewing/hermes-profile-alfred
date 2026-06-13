# Inbox Triage Skill

Classify Slack mentions and DMs by urgency, draft replies for the easy ones, escalate the rest.

## Parameters

- `lookback_hours` (integer, optional): How far back to scan. Default `24`.
- `include_channels` (array of strings, optional): Channel names or IDs to include. Default: only DMs and mentions, no channel scanning.
- `draft_replies` (boolean, optional): If true, draft a reply for each "quick" item. Default `false`.

## What it does

1. Pulls candidate messages via the `slack` MCP.
2. Buckets each into one of:
   - **Now:** explicit ask, deadline today, or follow-up older than 48 hours.
   - **Today:** ask with no deadline, but the sender expects same-day.
   - **This week:** FYI, async question, no clear deadline.
   - **Ignore:** thanks-only replies, automated notifications, bot chatter.
3. For `Now` and `Today` items where `draft_replies=true`, drafts a short reply in the principal's voice. Drafts are **drafts** — never sent automatically.

## Returns

```json
{
  "now": [{"sender": "...", "channel": "...", "permalink": "...", "summary": "...", "draft": "..."}],
  "today": [...],
  "this_week": [...],
  "ignored_count": 17
}
```

## Examples

- "Alfred, triage the inbox."
- "Triage the last 4 hours and draft replies."

## Limitations

- Urgency classification is heuristic. Always glance at the `now` bucket before acting on it.
- Drafts mirror the principal's tone from prior messages in the same thread. If there's no prior context, drafts default to Alfred's calm-and-concise voice — which may not match the principal's. Edit before sending.
- "Ignore" is a soft bucket. The skill counts ignored items but never deletes anything.
