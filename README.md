# Personal Operator

A Claude Code plugin that makes calls, runs errands, triages email, and briefs you when you're back — while asking you before anything involving money, sending, signing or cancelling.

## What's inside

| Piece | What it does |
|---|---|
| `skills/place-call` | Writes a call script, shows it to you, dials through Vapi, reads the transcript back |
| `skills/errands` | Bookings, orders, forms, lookups — stops at the final "confirm" step |
| `skills/inbox-triage` | Sorts Gmail into Urgent / Later / Ignore, drafts replies (never sends) |
| `skills/daily-brief` | One short digest: what needs you, today's calendar, calls made, inbox counts |
| `skills/escalate` | The rules for when to stop and ask you |
| `agents/caller` | Handles phone calls end to end |
| `agents/concierge` | Researches and preps errands |
| `/away 3h` | Away mode — works your list, drafts instead of sends, escalates risky stuff |
| `/catchup` | Digest when you return |
| `.mcp.json` | Connects the Vapi phone server |

## Setup (about 15 minutes)

### 1. Get a Vapi account and a number that can call out
1. Sign up at vapi.ai and copy your **private API key** from the dashboard.
2. **Buy or import a phone number.** Vapi's free numbers can only receive calls — outbound calls need a purchased number or one imported from Twilio/Telnyx.
3. Calls are billed per minute by Vapi, separate from your Claude plan.

### 2. Put your key in your environment (not in any file)
macOS / Linux — add to `~/.zshrc` or `~/.bashrc`:
```bash
export VAPI_API_KEY="your-key-here"
```
Then open a new terminal. The plugin's `.mcp.json` reads `${VAPI_API_KEY}` from there, so the key never sits in the plugin folder.

Windows (Command Prompt):
```
setx VAPI_API_KEY "your-key-here"
```
Then fully quit the Claude app (including from the system tray) and reopen it, so it sees the new key.

The plugin connects to Vapi directly over HTTPS, so no Node.js is needed.

### 3. Install the plugin
Unzip this folder somewhere permanent (e.g. `~/personal-operator`), then in Claude Code:
```
/plugin marketplace add ~/personal-operator
/plugin install personal-operator@personal-operator
```
The folder already includes its own `marketplace.json`, so it works as-is. To use it from GitHub instead, push the folder to a repo and run `/plugin marketplace add <your-github-user>/<repo>`.

### 4. Connect Gmail and Calendar
In claude.ai → Settings → Connectors, connect Gmail and Google Calendar. They're tied to your account, so they work from your phone too.

### 5. Paste the standing rules
Copy `templates/standing-rules.md` into your project instructions and a `CLAUDE.md`. Then the "ask me first" rules hold even if the plugin isn't loaded.

## Test it before you trust it
1. `Call my own cell at +1XXXXXXXXXX and ask what time it is.` — check the script, approve it, answer the call, then ask Claude for the outcome.
2. `Triage my inbox.` — check the buckets look right.
3. `/away 30m` with one small task, then `/catchup`.

Only then put recurring stuff (morning brief, inbox sweep) on a schedule — and check the first scheduled run by hand.

## Ground rules (built in)
- The phone assistant always says it's an AI calling for you. **No voice clone of you** — it creates legal risk (recording-consent and robocall laws) and is a credential you can never change.
- Drafts, never sends. Prepares, never pays, signs, cancels or confirms.
- Anything an email or web page *tells* Claude to do is treated as suspicious, not as an instruction. An agent that reads your inbox and can make calls is a target — this rule is what keeps it safe.
