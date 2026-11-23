# Required Credentials & Configuration

> **Security notice:** the original export had a HeyGen API key hardcoded in
> 4 nodes and a Kie.ai bearer token in 2 nodes. **Revoke and regenerate both
> keys** before using this workflow, even though they were removed from this
> repo. A key that has appeared in an export, chat or git history should be
> treated as compromised.

## 1. n8n credentials (create under *Credentials*)

| Credential name | n8n type | Used by |
|---|---|---|
| `OpenAI account` | OpenAI API | 3x OpenAI Chat Model (gpt-4o), script writers |
| `HeyGen account` | HeyGen API (community node `@heygenofficial/n8n-nodes-heygen-official`) | Create video agent / Get video status nodes |
| `Google Sheets account` | Google Sheets OAuth2 | Append row in sheet / sheet1 |
| `Google Drive account` | Google Drive OAuth2 | Upload file, Share file |

The original had two HeyGen credentials ("HeyGen account" and "HeyGen account 2").
You only need one; point both HeyGen nodes at it.

Install the community node first: *Settings -> Community Nodes ->
`@heygenofficial/n8n-nodes-heygen-official`*.

## 2. Environment variables (see `.env.example`)

| Variable | Purpose |
|---|---|
| `N8N_BLOCK_ENV_ACCESS_IN_NODE=false` | Allows `$env.*` in nodes |
| `HEYGEN_API_KEY` | `x-api-key` header on the HeyGen HTTP Request nodes |
| `KIE_API_KEY` | Bearer token for Kie.ai (Kling AI Avatar Pro) |
| `GOOGLE_DRIVE_FOLDER_ID` | Folder that receives uploaded avatar images |

## 3. Manual setup after import

- Re-select credentials on every node that shows a warning.
- Both **Append row in sheet** nodes have no spreadsheet selected; choose your
  sheet and map the columns.
- Both **If** nodes have empty conditions; set them to check the video status
  (e.g. HeyGen `data.status` equals `completed`, Kie.ai `data.state` equals `success`).
- Replace the placeholder `callBackUrl` (`https://your-domain.com/api/callback`)
  in **Request Avatar Video**, or remove it if you poll instead.
- Replace the hardcoded `avatar_id`, `voice_id` and sample `audio_url` with your own.

## 4. Accounts to prepare

1. HeyGen account with API access and credits.
2. Kie.ai account with credits (the Kling avatar branch).
3. OpenAI API key.
4. Google Cloud project with Drive and Sheets APIs enabled.
