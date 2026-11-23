# Required Credentials & Configuration

## 1. n8n credentials (Credentials menu in n8n)

| Credential type | Used by | Notes |
|---|---|---|
| Google Sheets OAuth2 | Get row(s) in sheet, Update row in sheet | Google Cloud project with the Sheets API enabled |
| Tavily API (community node `@tavily/n8n-nodes-tavily`) | Search | Key from tavily.com |
| OpenAI API | OpenAI Chat Model1, OpenAI Chat Model (`gpt-4.1`) | |
| Telegram API | Send a photo message, Send message and wait for response | Bot token from @BotFather |
| LinkedIn OAuth2 | Create a post | See "Known issues" in the README: this node is not configured yet |

## 2. Environment variables (see `.env.example`)

| Variable | Purpose |
|---|---|
| `N8N_BLOCK_ENV_ACCESS_IN_NODE=false` | Lets nodes read `$env.*` |
| `KIE_API_KEY` | Kie.ai key used by **Send Image Request** and **Get Image1** (HTTP nodes, `Authorization: Bearer ...`) |
| `LINKEDIN_SHEET_ID` | Google Sheet ID used by both Sheets nodes |
| `TELEGRAM_CHAT_ID` | Chat that receives the photo and the approval buttons |

## 3. Google Sheet layout

Sheet named whatever you like, first tab (`gid=0`), headers exactly:

`Topic ` (note the **trailing space**) | `Status` | `Text Post Content` | `Image Post URL`

Put `To Do` in Status for rows you want processed. The workflow writes `Created ` (also with a trailing space).

## 4. Security note

A live Kie.ai API key was hardcoded in two HTTP nodes of the original export, and the Telegram chat ID and sheet ID were embedded. All now come from `$env`. **Rotate the Kie.ai key** in your Kie.ai dashboard, since it was in the file you exported.
