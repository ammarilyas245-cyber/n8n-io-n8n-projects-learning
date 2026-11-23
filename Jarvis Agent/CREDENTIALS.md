# Required Credentials & Configuration

The exported workflow contains no credentials or API keys; they were not
included in the export. After importing, assign each of these on the nodes
that show a warning.

## 1. n8n credentials

| Credential type | Used by |
|---|---|
| Telegram API (bot token from @BotFather) | Telegram Trigger, Get a file, Send a text message, Send an audio file |
| OpenAI API | OpenAI Chat Model (`gpt-4.1-mini`) |
| ElevenLabs API (community node `@elevenlabs/n8n-nodes-elevenlabs`) | Transcribe audio or video, Convert text to speech |
| Gmail OAuth2 | Send Email, Reply to an Email, Get Emails, Add Label to Email, Get Labels, Draft Email, Draft Email Reply |
| Google Calendar OAuth2 | Check Availability, Get all Events, Delete Calendar Event, Reschedule Event, Get Event, Create an event |
| Google Tasks OAuth2 | Create / Get / Get many / Complete / Delete a Task |
| Google Sheets OAuth2 | Get all Expenses, Create Expense, Delete Expense |
| Google Contacts OAuth2 | Get Contacts |
| (Recommended) Bearer / Header Auth | MCP Server triggers and MCP Client nodes |

One Google Cloud project with the Gmail, Calendar, Tasks, Sheets and People APIs
enabled can cover all the Google credentials.

## 2. Environment variables (see `.env.example`)

| Variable | Purpose |
|---|---|
| `N8N_BLOCK_ENV_ACCESS_IN_NODE=false` | Lets nodes read `$env.*` |
| `N8N_BASE_URL` | Public URL of your n8n; MCP Client nodes call `<N8N_BASE_URL>/mcp/<path>/sse` |
| `TELEGRAM_ALLOWED_USERNAME` | The only Telegram username allowed to use the bot |
| `ASSISTANT_USER_NAME` | Your name, used in the prompt and email signatures |
| `EXPENSE_SHEET_ID` | Google Sheet ID of your expense tracker |
| `GOOGLE_TASKS_LIST_ID` | ID of your Google Tasks list (shared by all task nodes) |

## 3. Manual steps after import

- Change each MCP Server node's **Path** from its placeholder (`jarvis-*-mcp`) to a
  random string and update the matching MCP Client URL.
- Replace the calendar `user@example.com` and `Asia/Kolkata` timezone on the Calendar nodes.
- Pick your ElevenLabs voice on **Convert text to speech**.
- Expense sheet headers: `Date`, `Description`, `Category`, `Amount`.
