---
name: daily-brief
description: Use for the morning brief or when the user returns and asks what happened ("catch me up", "/catchup"). Combines today's calendar, urgent email, calls placed, errands in progress, and anything waiting on the user into one short digest.
---

# Daily brief

## Gather (silently, in parallel where possible)

1. **Calendar** — today's events (and tomorrow morning's, if it's evening). Flag overlaps and anything without a location or link.
2. **Email** — run `inbox-triage` and keep only the Urgent bucket and the Later count.
3. **Calls** — `list_calls` from Vapi since the last brief; `get_call` for each to get the outcome.
4. **Open items** — anything escalated or waiting for approval in this session.

If a source isn't connected, skip it and say so in one line at the bottom. Don't fail the whole brief.

## Write it

Short enough to read on a phone in under a minute. Lead with what needs the user.

```
NEEDS YOU (n)
1. <thing> — <why> — <what to do: approve / reply / decide>

TODAY
- 9:00  <event>  ⚠ overlaps with <event>
- ...

DONE WHILE YOU WERE OUT
- Called <business>: <outcome, confirmation #>
- Drafted reply to <sender> (in Gmail drafts)

INBOX: <n> urgent (above), <n> for later, <n> ignored

Not checked: <sources that weren't available>
```

If nothing needs the user, say "Nothing needs you right now." on the first line.

## Don't

- Don't pad it. No greeting paragraph, no motivational line.
- Don't take actions while briefing — just report and offer.
