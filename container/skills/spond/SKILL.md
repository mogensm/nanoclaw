---
name: spond
description: Read events, members, attendance, and messages from Spond
---

# Spond Skill — Club Event Management

Read events, attendance, members, and messages from Christiania Jaegerklub's Spond group. Uses the unofficial Spond REST API with email/password authentication.

**All commands below must be run using the Bash tool.**

## Commands

### List members

Run with the Bash tool:

```bash
node /workspace/extra/skills/spond/scripts/spond.js members
```

- Returns all members with name, email, phone, and subgroup membership.
- Sorted alphabetically by last name.

### List events

Run with the Bash tool:

```bash
node /workspace/extra/skills/spond/scripts/spond.js events [--count N] [--past]
```

- Default: 25 upcoming events, sorted by start time.
- `--past` shows past events instead (newest first).
- `--count N` limits the number of events returned.
- Shows attendance summary (accepted/declined/waiting/unanswered) and event ID.

### Get event detail

Run with the Bash tool:

```bash
node /workspace/extra/skills/spond/scripts/spond.js event --id <eventId>
```

- Use event IDs from the `events` output.
- Returns full event detail: date, location, description, attendance names, and comments.
- Searches both upcoming and past events.

### Read messages

Run with the Bash tool:

```bash
node /workspace/extra/skills/spond/scripts/spond.js messages [--count N]
```

- Default: 25 most recent chat threads.
- Shows chat name, last message, sender, and chat ID.

### Send message

Run with the Bash tool:

```bash
node /workspace/extra/skills/spond/scripts/spond.js send-message --text "..." --to <name>
node /workspace/extra/skills/spond/scripts/spond.js send-message --text "..." --chat <chatId>
```

- `--to` accepts a member's full name (e.g. "Ola Nordmann") or member ID.
- `--chat` replies to an existing chat thread by ID.
- **Sending requires elevated approval** — always confirm with the user before sending.

## Important Notes

- The group "Christiania Jaegerklub" is selected automatically by name.
- This uses an unofficial API — Spond has no official API.
- Authentication is via email/password (not API key). 2FA must be disabled on the Spond account.
- All data is read-only except `send-message`.

## Usage Guidelines

- To check upcoming events: `node /workspace/extra/skills/spond/scripts/spond.js events`
- To see who's attending an event: `node /workspace/extra/skills/spond/scripts/spond.js event --id <id>`
- To list members: `node /workspace/extra/skills/spond/scripts/spond.js members`
- Always show the user what you intend to send and get confirmation before executing `send-message`.
