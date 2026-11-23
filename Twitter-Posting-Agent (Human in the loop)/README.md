# X (Twitter) Post Automation with Human-in-the-Loop (n8n)

An n8n workflow that researches a topic, drafts a tweet, emails it to you for
approval, and posts it to X only after you say yes. If you ask for changes, an
AI revises the tweet and sends it back for another round.

## How it works

```
Form (Topic)
   -> X Post Agent  (web research via Tavily, writes the tweet)
   -> Set Post
   -> Gmail "Send and wait" (free-text reply from you)
   -> Text Classifier
        |-- Approved    -> Create Tweet (posts to X)
        '-- Disapproved -> Revision Agent (applies your feedback, can search again)
                              -> Set Post -> back to approval email
```

- **Trigger:** n8n form with one field, *Topic*.
- **X Post Agent:** always searches Tavily first, then writes a tweet under
  200 characters with a source mention and hashtags.
- **Approval:** an email asks "Good to Go?" and the workflow pauses until you reply in free text.
- **Text Classifier:** an LLM labels your reply *Approved* or *Disapproved*.
- **Revision Agent:** rewrites the post from your feedback (max 200 chars).
- **Create Tweet:** publishes the approved text.

## Tech stack

n8n, Tavily (web search), OpenAI (`gpt-5-mini` classifier), OpenRouter
(`nvidia/nemotron-3-ultra-550b-a55b:free` for both writing agents), Gmail, X API.

## Repository layout

```
.
├── workflows/x-post-human-in-the-loop.json   # sanitized export
├── .env.example
├── CREDENTIALS.md
├── LICENSE
└── README.md
```

## Setup

1. Prepare accounts and keys per [CREDENTIALS.md](CREDENTIALS.md).
2. Install the Tavily community node (`@tavily/n8n-nodes-tavily`).
3. `cp .env.example .env`, set `APPROVER_EMAIL`, and run n8n with
   `N8N_BLOCK_ENV_ACCESS_IN_NODE=false`.
4. Import `workflows/x-post-human-in-the-loop.json` and re-select credentials on each node.
5. Submit the form's test URL. Check the approval email, and test both an approval and a change request.

## Known limitations and suggestions

- **Posting depends on an LLM reading your free-text reply.** A reply like
  "don't post this" could be misread as approval. For safer gating, use
  Gmail's built-in *Approve / Disapprove* buttons or require an exact word.
- **No limit on revision rounds.** The loop continues until you approve.
- **Free OpenRouter models** are rate-limited and can change or disappear. Switch
  both writing agents to a stable model for production.
- **The 200-character cap** is conservative; X's limit is 280.
- **Sources in the tweet come from the model.** Check the "via ..." attribution
  before approving, since a model can misattribute.
- The classifier categories include trailing spaces (`"Approved "`). Harmless
  now, but easy to break if you compare them elsewhere.

## Security

No secrets are stored in this repo. API keys and OAuth tokens live in n8n's
encrypted credential store; the approver email comes from an environment variable.

## License

MIT, see [LICENSE](LICENSE). Replace `<YOUR NAME>` with your name.
