# Guardrail Agent (n8n)

A small n8n chat workflow that screens every user message with a keyword
guardrail before it reaches an AI model. Clean messages go to a helpful
assistant; flagged messages get a fixed, polite refusal.

## How it works

```
When chat message received
        |
     Guardrails  (keyword filter on chatInput)
     /        \
 passed       flagged
   |             |
Approved       Disapproved
Content Agent  Content Agent
(answers)      (fixed refusal)
```

- **Trigger:** n8n Chat Trigger (`When chat message received`).
- **Guardrails node:** blocks messages containing any keyword from a
  configurable list of sexual, profane and abusive terms.
- **Approved Content Agent:** friendly, concise assistant that answers the question.
- **Disapproved Content Agent:** returns one fixed refusal message without
  repeating or engaging with the flagged content.
- **Model:** OpenRouter `openai/gpt-oss-20b:free`, shared by both agents.

## Repository layout

```
.
├── workflows/guardrail-agent.json   # sanitized n8n export
├── .env.example
├── CREDENTIALS.md
├── LICENSE
└── README.md
```

## Setup

1. Create an OpenRouter API key and add it in n8n (see [CREDENTIALS.md](CREDENTIALS.md)).
2. Import `workflows/guardrail-agent.json` (*Workflows -> Import from File*).
3. Open the **OpenRouter Chat Model** node and select your credential.
4. Open the chat from the trigger node and test with a safe and a flagged message.

## Known limitations and suggestions

- **Keyword filtering is blunt.** It misses misspellings and paraphrases and can
  false-positive on harmless words in the list (e.g. "adult", "pleasure",
  "ass", "climax", "escort"). Consider adding an LLM-based or
  moderation-model check alongside it.
- **The refusal doesn't need an LLM.** The Disapproved branch calls a model to
  output a fixed string, which costs tokens and could drift from the script.
  A Set node returning the text is cheaper and deterministic. It also sends
  the flagged text to the model.
- **The approved agent's prompt says every message is "safe".** That is
  not guaranteed (the filter can miss things), so soften that wording to avoid
  weakening the model's own safety behaviour.
- The free OpenRouter model is rate limited and may change or be removed.

## Security

No secrets are stored in this repo. The OpenRouter key lives in n8n's encrypted
credential store. See [CREDENTIALS.md](CREDENTIALS.md).

## License

MIT, see [LICENSE](LICENSE). Replace `<YOUR NAME>` with your name.
