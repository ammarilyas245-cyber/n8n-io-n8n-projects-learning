# Required Credentials

The exported workflow contains no API keys. Credential references were reduced
to names, so re-select them after importing.

| Credential name | n8n credential type | Used by | How to get it |
|---|---|---|---|
| `OpenRouter account` | OpenRouter API | OpenRouter Chat Model (feeds both agents) | https://openrouter.ai/keys |

No other credentials are needed. The Chat Trigger and Guardrails nodes use none.

## Optional n8n settings

See `.env.example`. These configure your n8n instance and are not read by the workflow.
