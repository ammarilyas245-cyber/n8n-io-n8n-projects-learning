# Required Credentials

Create each credential in n8n (**Credentials → Create Credential**), then open the
listed nodes and select it. The exported workflow contains only credential *names*,
never secrets.

| # | Credential type (n8n) | Suggested name | Env variable(s) in `.env` | Where to get it | Used by nodes |
|---|---|---|---|---|---|
| 1 | Google Drive OAuth2 API | `Google Drive account` | `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET` | Google Cloud Console → APIs & Services → Credentials (enable Google Drive API) | Google Drive Trigger, Download file |
| 2 | Pinecone API | `Pinecone account` | `PINECONE_API_KEY` | https://app.pinecone.io | Pinecone Vector Store (insert), Pinecone Vector Store1 (retrieve tool) |
| 3 | OpenAI API | `OpenAI account` | `OPENAI_API_KEY` | https://platform.openai.com/api-keys | Embeddings OpenAI (shared by both Pinecone nodes) |
| 4 | OpenRouter API | `OpenRouter account` | `OPENROUTER_API_KEY` | https://openrouter.ai/keys | OpenRouter Chat Model |

## Placeholders to replace inside the workflow JSON

| Placeholder | Meaning | Env variable |
|---|---|---|
| `YOUR_GOOGLE_DRIVE_FOLDER_ID` | Drive folder watched by the trigger (pick it from the node's list after connecting Google Drive) | `GOOGLE_DRIVE_FOLDER_ID` |

## Other configuration

- **Pinecone index:** `n8n-14recipes-project`, **1536 dimensions**, matching the embeddings node.
- **Pinecone namespace:** `14 recipes Ingredients + Methods` (must be identical in the insert and retrieve nodes).
- **Chat model:** `nvidia/nemotron-3-super-120b-a12b` via OpenRouter; model availability and pricing may change.
- Google OAuth apps in "Testing" mode need your Google account added as a test user.
