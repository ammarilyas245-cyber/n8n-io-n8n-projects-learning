# Required Credentials & Configuration

The export contained **no API keys or secrets**. Only n8n credential references (internal IDs, no secret values), which were removed.

## 1. n8n credentials

| Credential type | Used by | Notes |
|---|---|---|
| OpenRouter API | OpenRouter Chat Model (`nvidia/nemotron-3-ultra-550b-a55b:free`) | Key from openrouter.ai |
| SerpApi (community node `n8n-nodes-serpapi`) | Google search in SerpApi (agent tool) | Key from serpapi.com |
| Header Auth (recommended) | Webhook | Not in the original; see below |

## 2. Manual steps after import

- Set the Webhook **Path** to a long random string (the placeholder is `REPLACE-WITH-RANDOM-STRING`). The original path was short and guessable.
- Add **Header Auth** to the Webhook node and send the same header from your Lovable app. Without it, anyone who finds the URL can run your agent and use your OpenRouter/SerpApi quota.
- Keep the token in a server-side secret (e.g. a Lovable edge function or backend), never in frontend code.
- Restrict CORS in the Webhook's Options to your Lovable app's origin.

## 3. Environment variables (`.env.example`)

Only runtime settings and reference placeholders; this workflow reads no `$env.*` values.
