# Credentials & Configuration Checklist

Everything you need before the **Customer Support Chatbot** workflow will run.

- **n8n credentials** hold OAuth tokens and API keys (created under *Credentials > Create*).
- **Environment variables** (`.env`) hold account-specific IDs that the nodes read with `{{ $env.NAME }}`.
- The workflow JSON contains **no secrets**. Credential entries in it are only name + ID references.

## 1. n8n credentials

| # | Service | n8n credential type | Used by node(s) | What you need | Where to get it |
|---|---|---|---|---|---|
| 1 | Gmail | `Gmail OAuth2 API` | Gmail Trigger, Add label to message, Reply to a message | OAuth Client ID + Secret; scopes to **read, modify labels and send** mail | Google Cloud Console (enable Gmail API) |
| 2 | Google Drive | `Google Drive OAuth2 API` | Google Drive Trigger, Download file | OAuth Client ID + Secret, Drive API enabled | Google Cloud Console (enable Drive API) |
| 3 | OpenAI | `OpenAI API` | OpenAI Chat Model (gpt-4.1-mini), Embeddings OpenAI | API key, billing enabled | platform.openai.com/api-keys |
| 4 | NVIDIA | `NVIDIA API` (NIM) | NVIDIA Nemotron Chat Model (email classifier) | API key (`nvapi-...`) | build.nvidia.com |
| 5 | Pinecone | `Pinecone API` | Pinecone Vector Store, Pinecone Vector Store1 | API key | app.pinecone.io > API Keys |

One Google OAuth client can be reused for credentials 1 and 2. Add your n8n callback URL (`https://<your-n8n-host>/rest/oauth2-credential/callback`) as an authorised redirect URI.

## 2. Environment variables (read via `{{ $env.NAME }}`)

| Variable | Used by | What it is |
|---|---|---|
| `PINECONE_RETRIEVE_INDEX` | Pinecone Vector Store (agent tool) | Index the agent searches |
| `PINECONE_INGEST_INDEX` | Pinecone Vector Store1 (ingestion) | Index new documents are written to. **Should equal the retrieve index** |
| `DRIVE_FAQ_FOLDER_ID` | Google Drive Trigger | Folder watched for new files |
| `DRIVE_PROFILE_FILE_ID` | Download file | File that is downloaded and ingested |
| `GMAIL_LABEL_ID` | Add label to message | Label applied to handled emails (`Label_...`) |
| `N8N_BLOCK_ENV_ACCESS_IN_NODE` | n8n setting | Must be `false` for `$env` to work (self-hosted) |

n8n Cloud does not expose `$env`. There, create **Variables** and replace `$env.` with `$vars.` in those five nodes, or pick the values from the dropdowns instead.

## 3. Setup items that are not credentials

- **Pinecone index** with a dimension that matches the embedding model in the *Embeddings OpenAI* node (1536 for `text-embedding-3-small`, the node default; check the model you selected), and namespace `AI Tech Company Information`
- **Google Drive folder** that holds your knowledge-base files (PDF)
- **Gmail label** to mark handled emails (create it in Gmail first, then copy its ID)
- n8n must be able to poll Gmail and Drive (both triggers run **every minute**)

## 4. Audit result for the exported file

| Check | Result |
|---|---|
| Hardcoded API keys, bearer tokens, passwords | **None found** |
| Credential data in the JSON | References only (IDs and names, no secrets) |

Personal data and account-specific values that were found and handled:

| Found | Where | Action taken |
|---|---|---|
| Two personal Gmail addresses, full email headers (delivery chain, mail-server IPs, message and thread IDs), Drive user name, permission ID and profile photo URLs | `pinData` of *Gmail Trigger* and *Google Drive Trigger* (saved test executions) | Removed all pinned data |
| Google Drive folder ID and file ID (and the file name / URLs cached next to them) | Google Drive Trigger, Download file | Replaced with `DRIVE_FAQ_FOLDER_ID`, `DRIVE_PROFILE_FILE_ID` |
| Gmail label ID | Add label to message | Replaced with `GMAIL_LABEL_ID` |
| Two Pinecone index names | Both Pinecone nodes | Replaced with `PINECONE_RETRIEVE_INDEX`, `PINECONE_INGEST_INDEX` |
| n8n `instanceId`, `webhookId`s | `meta`, Gmail nodes | Removed (n8n regenerates them) |

No keys need rotating for this file. Because the pinned data contained real email content, treat any earlier copy of the export that was shared or committed as having exposed those addresses.
