# Credentials & Configuration Checklist

Everything you need to create before the WhatsApp AI Assistant workflows will run.
Secrets are **never** stored in the workflow JSON files. They live in n8n's encrypted
credential store; non-secret IDs are read from environment variables (see `.env.example`).

## 1. n8n credentials (create under *Credentials > New*)

| # | Service | n8n credential type | Used by workflow(s) | Used by node(s) | What you need | Where to get it |
|---|---|---|---|---|---|---|
| 1 | WhatsApp Business Cloud (trigger) | `WhatsApp OAuth API` | Parent | WhatsApp Trigger | Meta App **Client ID** + **Client Secret** | developers.facebook.com > your app > Settings > Basic |
| 2 | WhatsApp Business Cloud (send / media) | `WhatsApp Business Cloud API` | Parent | Send message, Send message1, Download media (x2), Voice/Image Message Download (HTTP) | **Access Token** (use a permanent System User token) + **Business Account ID** | Meta Business Suite > System Users; WhatsApp > API Setup |
| 3 | OpenAI | `OpenAI API` | Parent, Weather, Research, Gmail Master | Whisper transcription, Analyze image (GPT-4.1-mini), Generate audio (TTS), Embeddings, Message a model (GPT-4.1), OpenAI Chat Model (GPT-5.1 / 4.1-mini) | API key | platform.openai.com/api-keys |
| 4 | NVIDIA | `NVIDIA API` (NVIDIA NIM) | Parent | NVIDIA Nemotron Chat Model (primary orchestrator LLM) | API key (`nvapi-...`) | build.nvidia.com |
| 5 | Groq | `Groq API` | Parent, Calendar, Google Sheets | Groq Chat Model, Model1, Model2, Model3 (fallback + sub-agents) | API key (`gsk_...`) | console.groq.com/keys |
| 6 | OpenRouter *(optional)* | `OpenRouter API` | Parent | OpenRouter Chat Model1 | API key | openrouter.ai/keys |
| 7 | Pinecone | `Pinecone API` | Parent | Pinecone Vector Store | API key | app.pinecone.io > API Keys |
| 8 | Tavily | `Tavily API` *(community node)* | Parent, Research | Search in Tavily | API key (`tvly-...`) | app.tavily.com |
| 9 | SerpApi | `SerpApi` *(community node)* | Parent, Research | Google search in SerpApi | API key | serpapi.com/manage-api-key |
| 10 | OpenWeatherMap | `OpenWeatherMap API` | Parent, Weather | OpenWeatherMap | API key | home.openweathermap.org/api_keys |
| 11 | Google Calendar | `Google Calendar OAuth2 API` | Parent, Calendar | Create / Get / Update / Delete event | OAuth Client ID + Secret; **write** scope | Google Cloud Console |
| 12 | Google Sheets | `Google Sheets OAuth2 API` | Parent, Google Sheets, Gmail Master | Get / Append / Update / Delete row(s), contact lookup | OAuth Client ID + Secret | Google Cloud Console |
| 13 | Gmail | `Gmail OAuth2 API` | Parent, Gmail Master | Get many messages, Reply to a message, Send a message | OAuth Client ID + Secret; read **and** send scopes | Google Cloud Console |
| - | Wikipedia | none | Parent, Research | Wikipedia tool | public API, no key | - |

Google OAuth setup: in Google Cloud Console enable the **Google Calendar API**, **Google Sheets API**
and **Gmail API**, create an OAuth client (Web application), and add your n8n callback URL
(`https://<your-n8n-host>/rest/oauth2-credential/callback`) as an authorised redirect URI.
One OAuth client can be reused for credentials 11-13.

> Credential **names/IDs** inside the exported JSON (e.g. "OpenAI account") are only labels from the
> original instance. After import, re-select your own credential on every node that shows a warning.

## 2. Environment variables (read by workflows via `{{ $env.NAME }}`)

| Variable | Replaces hardcoded value in | Example | Nodes affected |
|---|---|---|---|
| `WHATSAPP_PHONE_NUMBER_ID` | Send message / Send message1 | `1234567890123456` | 2 (parent) |
| `GOOGLE_CALENDAR_ID` | Calendar tool nodes | `you@example.com` | 4 (parent) + 4 (calendar sub-workflow) |
| `EMPLOYEE_SHEET_ID` | Google Sheets nodes | ID from the sheet URL | 5 (parent) + 4 (sheets sub-workflow) + 1 (gmail sub-workflow) |
| `EMPLOYEE_SHEET_TAB` | Google Sheets `sheetName` | `Sheet1` | same as above |
| `PINECONE_INDEX` | Pinecone Vector Store | `my-recipes-index` | 1 (parent) |
| `N8N_BLOCK_ENV_ACCESS_IN_NODE` | n8n setting | `false` | required for `$env` to work (self-hosted) |

n8n Cloud does not expose `$env`. There, create **Variables** and replace `$env.` with `$vars.` in the nodes above.

## 3. Community nodes & n8n settings

- Install: `@tavily/n8n-nodes-tavily`, `n8n-nodes-serpapi`
- Self-hosted: `N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true`
- n8n must be reachable over **HTTPS** so Meta can deliver webhooks

## 4. Non-credential setup items

- Google Sheet with tab `Sheet1` and header row: `Emplyee ID | Name | Age | Department | Email | Contact` (spelled exactly)
- Pinecone index populated with recipe embeddings using the same embedding model as the `Embeddings OpenAI` node
- Meta app: WhatsApp product enabled, phone number registered, webhook subscribed to `messages`
- Re-link the five *Call n8n Workflow Tool* nodes (Weather, Research, Calendar, Gmail Master, Google Sheets) to your imported sub-workflows

## 5. Rotate-before-publish list (found exposed in the original export)

| Item | Where it was | Action |
|---|---|---|
| WhatsApp/Meta access token (`EAAk...`) | Hardcoded `Authorization: Bearer` header in *Voice Message Download* and *Image Message Download* HTTP nodes | **Revoke in Meta and generate a new one** |
| Personal Gmail address | 4 Calendar nodes (parent) + 4 (calendar sub-workflow) + sticky note | Moved to `GOOGLE_CALENDAR_ID` |
| Google Sheet ID + URL (Employee Records) | 10 Sheets nodes across 3 workflows | Moved to `EMPLOYEE_SHEET_ID` |
| WhatsApp Phone Number ID | 2 WhatsApp send nodes | Moved to `WHATSAPP_PHONE_NUMBER_ID` |
| n8n `instanceId`, trigger `webhookId`s | `meta` / trigger nodes | Removed (n8n regenerates on import) |
