# Lovable ↔ n8n AI Agent

A minimal n8n workflow that exposes an AI agent as a webhook, so a web app built with [Lovable](https://lovable.dev) can send a question and get a short, friendly answer back, with live web search when needed.

## How it works

```
Lovable app ──POST {question}──▶ Webhook ──▶ AI Agent ──▶ Respond to Webhook ──▶ app
                                               ├─ OpenRouter Chat Model (nvidia/nemotron-3-ultra-550b-a55b:free)
                                               └─ SerpApi Google search (tool)
```

1. **Webhook** (POST) receives JSON with a `question` field.
2. **AI Agent** answers `{{ $json.body.question }}`, keeping replies short and friendly. It calls the SerpApi tool when the question needs real-time information.
3. **Respond to Webhook** returns the agent's output to the caller.

## Setup

1. Install the community node `n8n-nodes-serpapi`.
2. Import `workflows/lovable-n8n-ai-agent.json`.
3. Create the OpenRouter and SerpApi credentials (see `CREDENTIALS.md`) and assign them.
4. Set a random webhook path and add header authentication (see `CREDENTIALS.md`).
5. Activate the workflow and use the **Production** URL in your Lovable app.

Test:

```bash
curl -X POST "https://your-n8n-domain/webhook/<your-path>" \
  -H "Content-Type: application/json" \
  -H "X-Webhook-Token: <your-token>" \
  -d '{"question": "What is the weather in Karachi today?"}'
```

## Known limitations

- **No memory:** each request is independent; add a Window Buffer Memory node keyed by a session ID if you want conversations.
- **Free model:** `:free` OpenRouter models are rate-limited and can change or disappear.
- **Webhook is unauthenticated by default** and CORS is not restricted; see `CREDENTIALS.md`.
- **No input validation:** a missing `question` field sends an empty prompt to the model.
- **Prompt typos:** the system prompt contains "assistent"; harmless, but easy to fix.

## Security

No secrets are in this repository.

## License

MIT, see `LICENSE`.
