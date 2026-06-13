# Reminders Skill

Schedule and surface reminders for the principal.

## Parameters

- `action` (string, required): `"create"`, `"list"`, `"snooze"`, or `"dismiss"`.
- `topic` (string, required for `create`): What the reminder is about.
- `when` (string, required for `create` and `snooze`): A natural-language time ("tomorrow 9am", "in 30 minutes", "next Tuesday morning") or ISO-8601 timestamp.
- `channel` (string, optional): Where to deliver — `"slack-dm"`, `"system-notify"`, or `"chat"` (next time the principal opens an Alfred session). Default `"chat"`.
- `id` (string, required for `snooze` and `dismiss`): The reminder ID returned by `create` or `list`.

## What it does

Reminders are stored in `~/Documents/alfred/reminders.jsonl` via the `filesystem` MCP. Each line is:

```json
{
  "id": "r_abc123",
  "topic": "...",
  "due": "2026-06-13T09:00:00-07:00",
  "channel": "slack-dm",
  "status": "pending"
}
```

The `morning-briefing` and `evening-recap` cron jobs read this file and surface anything due in their window. The `slack` MCP is used for `slack-dm` delivery.

## Returns

- `create` → the created reminder object including its `id`.
- `list` → array of pending reminders, sorted by `due` ascending.
- `snooze` / `dismiss` → the updated reminder object.

## Examples

- "Remind me to call the bank tomorrow at 10."
- "What reminders do I have outstanding?"
- "Snooze that bank reminder until Friday."

## Limitations

- No live push delivery on its own — relies on cron jobs to fire at the right time. If Hermes isn't running when a reminder is due, it surfaces the next time briefing runs.
- Natural-language time parsing happens in the model, not a deterministic parser. Always confirm the resolved time with the principal before saving.
