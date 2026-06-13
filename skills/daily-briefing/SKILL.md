# Daily Briefing Skill

Compose a short morning or evening briefing for the principal.

## Parameters

- `window` (string, required): `"morning"` or `"evening"`.
- `timezone` (string, optional): IANA timezone. Defaults to `ALFRED_TIMEZONE`.
- `lookback_hours` (integer, optional): How far back to look at Slack/inbox activity. Default `14` for morning, `8` for evening.

## What it does

1. Pulls Slack mentions and DMs from the lookback window via the `slack` MCP.
2. Reads `~/Documents/alfred/calendar.md` (or any file the principal designates) via the `filesystem` MCP for upcoming events.
3. Reads `~/Documents/alfred/standing-orders.md` for recurring requests (e.g. "always remind me about Friday standup").
4. Produces a markdown briefing with this structure:
   - **Top of the day:** the single most time-sensitive item.
   - **Schedule:** events in the next 12 (morning) or 36 (evening) hours, in principal-local time.
   - **Inbox:** Slack mentions + DMs that look like they need a response, grouped by sender.
   - **Standing orders:** anything the principal asked to be reminded of today.
   - **Quiet:** one line acknowledging anything the principal asked Alfred to _not_ surface (so they know nothing was hidden).

## Returns

A markdown string. Does not send or post anywhere — the orchestrating cron job or chat session decides what to do with it.

## Examples

- "Alfred, briefing." → `window=morning`
- "What does the rest of the day look like?" → `window=evening`, `lookback_hours=4`

## Limitations

- Calendar source is a markdown file, not a live calendar API. Keep `calendar.md` updated, or swap in a calendar MCP and update this skill.
- Briefing trusts that the principal has scoped the Slack token to channels they actually want surfaced.
