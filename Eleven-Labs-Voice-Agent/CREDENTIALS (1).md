# Credentials & Configuration Checklist

Everything you need before the **ElevenLabs Voice Email Agent** workflow will run.

- **n8n credentials** hold OAuth tokens and API keys (created under *Credentials > Create*).
- **Environment variables** (`.env`) hold the sender name that the AI signs emails with.
- The workflow JSON contains **no secrets**. Credential entries in it are only name + ID references.

## 1. n8n credentials

| # | Service | n8n credential type | Used by node(s) | What you need | Where to get it |
|---|---|---|---|---|---|
| 1 | Groq | `Groq API` | Groq Chat Model (`llama-3.3-70b-versatile`) | API key (`gsk_...`) | console.groq.com/keys |
| 2 | Gmail | `Gmail OAuth2 API` | Send a message | OAuth Client ID + Secret; **send** scope | Google Cloud Console (enable Gmail API) |
| 3 | Google Contacts | `Google Contacts OAuth2 API` | Google Contacts (agent tool) | OAuth Client ID + Secret; People/Contacts API enabled | Google Cloud Console (enable People API) |
| 4 *(recommended)* | Webhook protection | `Header Auth` | Webhook | Header name (e.g. `X-Webhook-Token`) + a long random value | Create it yourself; paste the same value into the ElevenLabs tool's request header |

One Google OAuth client can be reused for credentials 2 and 3. Add your n8n callback URL (`https://<your-n8n-host>/rest/oauth2-credential/callback`) as an authorised redirect URI.

## 2. Environment variables (read via `{{ $env.NAME }}`)

| Variable | Used by | What it is |
|---|---|---|
| `SENDER_NAME` | AI Agent system prompt (2 places) | Name used in the sign-off, for example `Your Name` |
| `N8N_BLOCK_ENV_ACCESS_IN_NODE` | n8n setting | Must be `false` for `$env` to work (self-hosted) |

n8n Cloud does not expose `$env`. There, create a Variable and replace `$env.` with `$vars.` in the AI Agent system prompt, or type the name directly.

## 3. Outside n8n (ElevenLabs side)

The workflow never calls ElevenLabs. ElevenLabs calls the workflow, so nothing from ElevenLabs is stored in n8n. You need:

| Item | Notes |
|---|---|
| ElevenLabs account with a Conversational AI agent | elevenlabs.io |
| A **webhook (server) tool** on that agent | Method `POST`, URL = your n8n **Production URL** for the Webhook node, JSON body with `to` and `emailcontent` (see README) |
| The shared header secret | Same value as credential 4, if you enable Header Auth |
| `ELEVENLABS_API_KEY` | Only if you manage the agent through the ElevenLabs API |

## 4. Webhook URL and path

The Webhook node path was a random UUID in the original export. It is the only thing that kept the endpoint private, so it was replaced with the placeholder `REPLACE-WITH-A-RANDOM-UUID`. **Change it to a fresh random UUID** (or let n8n generate one) and enable Header Auth. Do not publish your real production URL.

## 5. Audit result for the exported file

| Check | Result |
|---|---|
| Hardcoded API keys, bearer tokens, passwords | **None found** |
| Credential data in the JSON | References only (IDs and names, no secrets) |
| Pinned test data | None present |

Items found and handled:

| Found | Where | Action taken |
|---|---|---|
| Webhook path (random UUID, effectively a secret URL) | Webhook | Replaced with a placeholder |
| Personal name used for the email sign-off, twice | AI Agent system prompt | Replaced with `{{ $env.SENDER_NAME }}`; the schema example now says `Your Name` |
| n8n `instanceId` and `webhookId`s | `meta`, Webhook and Gmail nodes | Removed (n8n regenerates them) |
| Example email addresses (`brandonstark345@gmail.com`) | Prompt and schema example | Left as is: fictional sample data |

No keys need rotating for this file.
