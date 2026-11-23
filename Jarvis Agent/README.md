# Jarvis: Telegram Personal Assistant (n8n)

A personal AI assistant that lives in Telegram. Send it a text or a voice note
and it can manage your email, calendar, tasks, contacts and expenses, then reply
in text or in voice.

## How it works

```
Telegram Trigger
  -> Only allow me (username allow-list)
  -> Text or voice?
       |- text  -> Jarvis agent
       '- voice -> download file -> ElevenLabs speech-to-text -> Jarvis agent

Jarvis agent (OpenAI gpt-4.1-mini + window memory + Think tool)
  uses five MCP tool servers:
    Gmail MCP        send, reply, draft, search emails, labels
    Calendar MCP     check availability, create/update/delete events
    Google Tasks MCP create, get, complete, delete tasks
    Finance Manager  expenses logged in a Google Sheet (read/add/delete)
    Google Contacts  look up names and email addresses

Reply -> Set Reply Message -> text or voice?
       |- text  -> Telegram text message (MarkdownV2)
       '- voice -> ElevenLabs text-to-speech -> Telegram audio message
```

The five **MCP Server** trigger nodes and their tool nodes live in the same
workflow; the agent connects to them through the **MCP Client** nodes using your
n8n instance's own `/mcp/<path>/sse` URLs.

## Tech stack

n8n, Telegram Bot API, OpenAI, ElevenLabs, Gmail, Google Calendar, Google Tasks,
Google Sheets, Google Contacts, Model Context Protocol (MCP).

## Repository layout

```
.
├── workflows/jarvis-telegram-assistant.json   # sanitized export
├── .env.example
├── CREDENTIALS.md
├── LICENSE
└── README.md
```

## Setup

1. Prepare accounts per [CREDENTIALS.md](CREDENTIALS.md).
2. `cp .env.example .env` and fill it in. Run n8n with `N8N_BLOCK_ENV_ACCESS_IN_NODE=false`.
3. Import `workflows/jarvis-telegram-assistant.json`.
4. Assign credentials on every node that shows a warning (Gmail, Calendar, Tasks,
   Sheets, Contacts, Telegram, OpenAI, ElevenLabs).
5. **Secure the MCP endpoints (see below) before activating.**
6. Create the expense sheet with columns `Date`, `Description`, `Category`, `Amount`.
7. Message your bot from your allowed Telegram account.

## Security: read before activating

- **The MCP server endpoints have no authentication.** Anyone who knows an
  endpoint URL can call tools that send email as you, delete calendar events and
  clear sheet rows. The import uses placeholder paths such as `jarvis-gmail-mcp`,
  which are **guessable**. Before activating, change each MCP Server node's *Path*
  to a long random string and enable *Bearer* authentication on both the server
  and client nodes. Update the matching MCP Client URL after changing a path.
- **Prompt injection.** Jarvis reads your email and acts on tools. A malicious
  email could try to instruct it to send messages or delete data. Keep
  destructive tools (delete event/task/expense, send email) behind a confirmation step.
- **The allow-list uses the Telegram username**, which can change or be reassigned.
  Matching on your numeric Telegram user ID is safer.
- The prompt says to confirm sensitive actions, but nothing in the workflow forces it.

## Known limitations

- The agent errors are passed straight back to you in Telegram (`output || error`).
- The speech-to-text and text-to-speech default to a Hindi voice; change the
  ElevenLabs voice for other languages.
- The calendar and timezone nodes still say `Asia/Kolkata` and the calendar
  `user@example.com`; set your own calendar and timezone.
- The notes on the **Create Expense** and **Delete Expense** nodes are swapped.
- Window memory is keyed by Telegram username and is held in n8n memory only.

## Attribution and license

This workflow looks like it was adapted from a published "Jarvis" template.
If it is based on someone else's work, check that template's license and credit
the original author here before publishing. Your own additions are under MIT,
see [LICENSE](LICENSE). Replace `<YOUR NAME>` with your name.
