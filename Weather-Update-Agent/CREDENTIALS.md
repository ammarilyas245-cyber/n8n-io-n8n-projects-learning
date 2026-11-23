# Credentials Checklist

Everything you must create in n8n (*Credentials > Create*) for this project.
The workflow JSON files contain only credential **references** (name + ID), never secrets.
After importing, open each node showing a credential warning and select your own credential.

## Weather tool (`weather-tool/workflow.json`)

| # | Service | n8n credential type | Node | What you need | Where to get it |
|---|---|---|---|---|---|
| 1 | OpenWeatherMap | `OpenWeatherMap API` | OpenWeatherMap | API key (free tier works) | home.openweathermap.org/api_keys |
| 2 | OpenAI | `OpenAI API` | Message a model (GPT-4.1) | API key with billing enabled | platform.openai.com/api-keys |

## Parent workflow (`parent-workflow/workflow.json`)

| # | Service | n8n credential type | Used for | What you need | Where to get it |
|---|---|---|---|---|---|
| 3 | WhatsApp Business Cloud | `WhatsApp Business Cloud API` | Sending replies | Permanent access token + Business Account ID | Meta Business Suite > System Users; developers.facebook.com > WhatsApp > API Setup |
| 4 | WhatsApp OAuth | `WhatsApp OAuth API` | WhatsApp trigger (webhook) | Meta App Client ID + Client Secret | developers.facebook.com > your app > Settings > Basic |
| 5 | LLM for the agent | depends on your model | Parent AI agent | API key for the chat model you choose | Your model provider |

Add one row here for each additional tool sub-workflow you create.

## Non-secret settings

| Item | Where | Notes |
|---|---|---|
| WhatsApp Phone Number ID | Parent workflow send node | Not a secret, but instance-specific; set your own |
| Public HTTPS URL for n8n | n8n config (`WEBHOOK_URL`) | Meta only delivers webhooks to HTTPS endpoints |
| Sub-workflow links | Parent agent tool nodes | Workflow IDs differ per instance; re-select after import |

## Audit result for the files reviewed

| Check | Result |
|---|---|
| Hardcoded API keys / bearer tokens | None found |
| Hardcoded emails, phone numbers, sheet IDs | None found |
| Credential data in the JSON | References only (IDs and names, no secrets) |
| n8n `instanceId` in `meta` | Found and removed |
