# Required Credentials & Configuration

The exported workflow contains no API keys. Credential references were reduced
to names, so re-select them after importing.

## 1. n8n credentials (create under *Credentials*)

| Credential name | n8n type | Used by | Where to get it |
|---|---|---|---|
| `Google Drive account` | Google Drive OAuth2 | Google Drive Trigger, Download file | Google Cloud Console: enable Drive API, create OAuth client |
| `Google Sheets account` | Google Sheets OAuth2 | Append row in sheet | Same project: enable Sheets API |
| `Gmail account` | Gmail OAuth2 | Send a message | Same project: enable Gmail API |
| `NVIDIA Nemotron account` | NVIDIA API | NVIDIA Nemotron Chat Model | https://build.nvidia.com (model `nvidia/llama-3.3-nemotron-super-49b-v1.5`) |
| `OpenAI account` | OpenAI API | Email Expert (`gpt-4o`, JSON schema output) | https://platform.openai.com/api-keys |

## 2. Environment variables (see `.env.example`)

| Variable | Purpose |
|---|---|
| `N8N_BLOCK_ENV_ACCESS_IN_NODE=false` | Lets nodes read `$env.*` |
| `INVOICE_DRIVE_FOLDER_ID` | Folder to watch (last part of the folder URL) |
| `INVOICE_SHEET_ID` | Invoice log sheet (between `/d/` and `/edit` in the sheet URL); also used for the link in the email |
| `BILLING_TEAM_EMAIL` | Recipient of the notification email |

## 3. Google Sheet headers

`Invoice Number ` , `Sender Name`, `Sender Email`, `Sender Address ` ,
`Total Amount ` , `Invoice Date`, `Due Date ` (some intentionally end in a space; copy them exactly).
