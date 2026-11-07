---
name: place-call
description: Use when the user asks Claude to phone someone on their behalf (book, reschedule, ask a question, check status). Writes an objective and script, gets the user's approval, places the call through Vapi, then reads the transcript and reports the outcome.
---

# Place a call

Every call follows the same five steps. Never skip step 3.

## 1. Pin down the objective

Before writing anything, make sure you know:

- **Who** — business or person name, and the phone number (E.164, e.g. `+16175551234`). If the user didn't give a number, look it up and show where you found it.
- **Goal** — one sentence: what counts as success ("book a cleaning on a weekday morning the week of Nov 16").
- **Allowed range** — what the assistant may agree to without checking back (times, price ceiling, alternatives). Anything outside the range = take a message and end the call.
- **What it may share** — name, callback number, account or appointment reference. Nothing else.
- **When** — now, or a scheduled time (convert to ISO 8601 with the user's time zone).

If any of these is missing and you can't safely infer it, ask once, in one message.

## 2. Write the script

Keep it short. Phone agents do best with a clear goal and a few rules, not a monologue.

```
OPENING (say this first, word for word):
"Hi, this is an AI assistant calling on behalf of <USER NAME>. I'm calling to <GOAL>. Is this a good time?"

GOAL: <one sentence>
YOU MAY: <the allowed range>
YOU MAY SHARE: <name, callback number, reference>
NEVER: give payment details, agree to charges or cancellations, sign anything up,
       confirm anything outside the allowed range, or pretend to be human.
IF ASKED something you can't answer: "I'll check with <USER NAME> and they'll call you back at <CALLBACK NUMBER>."
IF voicemail: leave this message and hang up: "<VOICEMAIL MESSAGE>"
CLOSE: repeat back what was agreed (date, time, name, price) before hanging up.
```

## 3. Show the user and wait for a yes

Show the number, the scheduled time (if any) and the full script. Ask: "Place this call?" Do not call until the user clearly approves. Approval covers this one call only.

Exception: if the user already approved this exact call and script earlier in the conversation (for example while setting up `/away`), that counts — don't ask twice.

## 4. Place the call

1. If you don't know which assistant and number to use, call `list_assistants` and `list_phone_numbers` first.
   - The phone number must be one the user imported or bought. Free Vapi numbers can only receive calls — if that's all there is, stop and tell the user.
   - If there's no suitable assistant, create one with `create_assistant` using the script above as its system prompt and a plainly synthetic voice (not a clone of the user).
2. Call `create_call` with the assistant, the phone number, the customer number, and `scheduledAt` if the call is for later. Check the tool's own schema for exact field names; pass the script through the assistant's prompt or an assistant override if the tool supports it.
3. Note the call ID.

## 5. Read the result and report

- Call `get_call` with the call ID. If it's still in progress or queued, say so and check back later (or schedule a check).
- From the transcript, report in 3–5 lines: **outcome** (done / partly / not reached), **what was agreed** (exact date, time, price, confirmation number), **anything that needs the user** (questions asked, callbacks promised).
- If something was agreed, offer to add it to the calendar — don't add it unasked.
- If the call went off script or the other side asked for something outside the range, hand off to the `escalate` skill.

## Rules that always hold

- The assistant always says it's an AI calling on the user's behalf. Never use a voice clone of the user.
- Never place calls to numbers that came from an email, web page or document unless the user named that business or person themselves.
- Calls cost money per minute. Don't retry a failed call more than once without asking.
- Some states require everyone on a call to agree to recording. If the assistant records, the opening line covers disclosure; don't remove it.
