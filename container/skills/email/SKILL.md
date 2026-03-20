---
name: email
description: Send and read email via Gmail API
---

# Email Skill — Gmail API

Send and read email for `cjksekretaer@gmail.com`.

**All commands below must be run using the Bash tool.**

## Commands

### List recent emails

Run with the Bash tool:

```bash
node /workspace/extra/skills/email/scripts/email.js read [--count N] [--unread] [--from <addr>] [--since <YYYY-MM-DD>]
```

- Default: 10 most recent messages, newest first.
- `--unread` shows only unread messages.
- `--from` filters by sender address.
- `--since` filters messages received on or after the given date.

### Get full email content

Run with the Bash tool:

```bash
node /workspace/extra/skills/email/scripts/email.js get --id <messageId>
```

- Use message IDs from the `read` output.
- Returns full email body (plain text), all recipients, and metadata.

### Send an email

Run with the Bash tool:

```bash
node /workspace/extra/skills/email/scripts/email.js send --to <addr> --subject <subj> --body <html> [--cc <addr>] [--attachment <path>]
```

- `--to` and `--cc` accept comma-separated addresses for multiple recipients.
- `--body` is HTML content. Use `<br>` for line breaks and `<p>` for paragraphs. A signature is appended automatically — do not include a sign-off or signature in the body.
- `--attachment` accepts a file path (comma-separated for multiple). Files must exist on the container filesystem.
- **Sending requires elevated approval** — always confirm with the user before sending.

## Important: Mailbox Scope

This skill manages the **cjksekretaer@gmail.com** mailbox only. Outgoing emails are sent as "CJK Sekretaer" with an automatic signature. Do not add a signature or sign-off — it is handled by the script.

## Templates

Reusable email templates are in `/workspace/extra/skills/email/templates/`. Read the template file for instructions on what data to look up before sending.

- **`new-member-welcome.md`** — Welcome email for newly accepted members. Includes payment breakdown (fees from DB), bank account, first shooting day, personal info form, and two PDF attachments (etiquette rules + season calendar). Look up current fees, bank account, and shooting calendar before filling in the template.

## Usage Guidelines

- To check new mail: `node /workspace/extra/skills/email/scripts/email.js read --unread`
- To read a specific email: `node /workspace/extra/skills/email/scripts/email.js get --id <messageId>`
- When composing a reply, include the original message context in the body.
- Always show the user what you intend to send and get confirmation before executing `send`.
