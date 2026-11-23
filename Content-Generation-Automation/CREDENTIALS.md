# Credentials & Configuration Checklist

Everything you need before **Content Generation Automation** will run end to end.

- **n8n credentials** hold OAuth tokens and API keys for native n8n nodes (created under *Credentials > Create*).
- **Environment variables** (`.env`) hold the Kie.ai and fal.ai keys, which the HTTP Request nodes read with `{{ $env.NAME }}`, plus the Google Sheet IDs.
- The workflow JSON contains **no secrets**. Credential entries in it are only name + ID references.

## 1. n8n credentials

| # | Service | n8n credential type | Used by node(s) | What you need | Where to get it |
|---|---|---|---|---|---|
| 1 | OpenAI | `OpenAI API` | OpenAI Chat Model, OpenAI Chat Model1 (GPT-4.1 prompt agents) | API key, billing enabled | platform.openai.com/api-keys |
| 2 | OpenRouter | `OpenRouter API` | OpenRouter Chat Model (Scene Generation agent) | API key | openrouter.ai/keys |
| 3 | Google Sheets | `Google Sheets OAuth2 API` | All 10 "Append row in sheet" nodes | OAuth Client ID + Secret, Sheets API enabled | Google Cloud Console |
| 4 | Telegram | `Telegram API` | Send message and wait for response (approval step) | Bot token **and** the target Chat ID | @BotFather on Telegram |
| 5 | YouTube | `YouTube OAuth2 API` | Upload a video | OAuth Client ID + Secret, YouTube Data API v3 enabled | Google Cloud Console |
| 6 | Instagram | Credential of the community node `@mookielianhd/n8n-nodes-instagram` | Publish (Reels) | Instagram Graph API token + business account ID (see the node's docs) | developers.facebook.com |

> **Heads-up:** in the exported file the **Telegram, Instagram and YouTube nodes have no credential attached** and the Telegram node has no Chat ID set. Attach your own credentials (and set the Chat ID) before running the approval and publishing steps.

## 2. Environment variables (read via `{{ $env.NAME }}`)

| Variable | Used by | Count | How to get it |
|---|---|---|---|
| `KIE_API_KEY` | All Kie.ai HTTP nodes: `createTask` and `recordInfo` (image + video) | 20 nodes | kie.ai dashboard > API Key |
| `FAL_API_KEY` | `FFmpeg Merge Videos {Fal.ai}` (format `key_id:key_secret`) | 1 node | fal.ai dashboard > Keys |
| `VIDEO_URLS_SHEET_ID` | Sheets nodes logging scene videos | 4 nodes | ID in the sheet URL |
| `IMAGE_URLS_SHEET_ID` | Sheets nodes logging carousel images | 5 nodes | ID in the sheet URL |
| `CONTACTS_SHEET_ID` | Leftover test branch (`Append row in sheet`) | 1 node | ID in the sheet URL, or delete the node |
| `N8N_BLOCK_ENV_ACCESS_IN_NODE` | n8n setting, must be `false` for `$env` to work | - | self-hosted only |

n8n Cloud does not expose `$env`. There, create **Variables** and replace `$env.` with `$vars.` in those nodes, or use an n8n **Header Auth** credential for Kie.ai and fal.ai instead.

> Anyone who can edit workflows on your n8n instance can read `$env` values. For shared instances prefer Header Auth credentials.

## 3. Community nodes & n8n settings

- Install `@mookielianhd/n8n-nodes-instagram` (*Settings > Community Nodes*)
- Form Trigger nodes need your n8n instance reachable at its public URL
- Telegram *Send and Wait* needs n8n reachable over HTTPS so Telegram can call back

## 4. Google Sheets to create

Each sheet needs a tab named **`Sheet1`** and the exact header row below (the workflow matches on the names, including trailing spaces).

| Sheet (suggested name) | Env variable | Header row |
|---|---|---|
| Ai Video URLs | `VIDEO_URLS_SHEET_ID` | `Date of Creation ` · `Task ID` · `Video URLs` · `Model` · `Credits Consumed ` (note the trailing space after *Creation* and *Consumed*) |
| Image Generation | `IMAGE_URLS_SHEET_ID` | `Image URL` |
| Contacts (optional) | `CONTACTS_SHEET_ID` | `Full Name` · `Email` · `What are you looking for ?` |

## 5. Audit result for the exported file

| Found in the original export | Where | Action taken |
|---|---|---|
| **Kie.ai API key**, hardcoded `Authorization: Bearer ...` | 20 HTTP Request nodes | Replaced with `{{ $env.KIE_API_KEY }}` |
| **fal.ai API key**, hardcoded `Authorization: Key ...` | `FFmpeg Merge Videos {Fal.ai}` | Replaced with `{{ $env.FAL_API_KEY }}` |
| 3 Google Sheet IDs and URLs | 10 Google Sheets nodes | Replaced with `{{ $env.*_SHEET_ID }}` |
| Pinned execution data (`pinData`): sample outputs and temporary media URLs | 25 nodes | Removed |
| n8n `instanceId` and the `webhookId`s on Wait and trigger nodes (several Wait nodes shared one ID) | `meta` and nodes | Removed (n8n regenerates them) |

**Rotate before publishing:** the Kie.ai and fal.ai keys were exposed in the original export. Revoke both and create new ones. If the old file was ever committed or shared, treat the keys as compromised, since deleting them from the latest commit does not remove them from Git history.
