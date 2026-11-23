# Required Credentials & Configuration

The exported workflow contains no API keys. Credential references were reduced
to names, so re-select them after importing.

## 1. n8n credentials (create under *Credentials*)

| Credential name | n8n type | Used by | Where to get it |
|---|---|---|---|
| `Tavily account` | Tavily API (community node) | Search in Tavily, Search in Tavily1 | https://app.tavily.com |
| `Gmail account` | Gmail OAuth2 API | Send message and wait for response | Google Cloud Console: enable Gmail API, create OAuth client |
| `OpenAI account` | OpenAI API | OpenAI Chat Model1 (Text Classifier, `gpt-5-mini`) | https://platform.openai.com/api-keys |
| `OpenRouter account` | OpenRouter API | OpenRouter Chat Model, Chat Model1 (both agents) | https://openrouter.ai/keys |
| `X account 2` | X (Twitter) OAuth2 API | Create Tweet | https://developer.x.com: create an app with OAuth2 and `tweet.read`, `tweet.write`, `users.read`, `offline.access` scopes |

Install the community node first: *Settings -> Community Nodes ->
`@tavily/n8n-nodes-tavily`*.

## 2. Environment variables (see `.env.example`)

| Variable | Purpose |
|---|---|
| `N8N_BLOCK_ENV_ACCESS_IN_NODE=false` | Lets nodes read `$env.*` |
| `APPROVER_EMAIL` | Address that receives the "Approval Required" email |

## 3. Notes

- The X API write access may require a paid X developer tier; check current limits.
- Gmail's *Send and wait* needs your n8n instance to be reachable at a URL
  that your reply link can hit (set `WEBHOOK_URL` when self-hosting).
