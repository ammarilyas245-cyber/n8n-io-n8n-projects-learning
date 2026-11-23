# LinkedIn Post Agent (n8n)

An n8n workflow that turns a topic in a Google Sheet into a researched LinkedIn post with a matching AI-generated graphic, sends both to you on Telegram for approval, and logs the result back to the sheet.

**Topic → Research → Write post → Generate image → Telegram approval → Log / Publish**

## How it works

1. **Triggers**: Manual trigger (testing) or Schedule Trigger (daily at 19:00).
2. **Get row(s) in sheet**: reads rows where `Status = To Do`.
3. **Search (Tavily)**: top 3 news results for "Latest developments in {topic} 2026".
4. **Generate Text AI Agent (GPT-4.1)**: writes one post (max 700 chars, 2–4 hashtags, 1–3 emojis, no invented facts).
5. **Generate Prompt AI Agent (GPT-4.1 + Structured Output Parser)**: converts the post into a detailed image prompt (headline, foreground/background, palette, layout).
6. **Send Image Request → Wait → Get Image1 → Switch6**: submits the prompt to Kie.ai (`nano-banana-pro`) and polls until `success`; `failed` ends the branch.
7. **Code in JavaScript6 → Download Image1**: extracts the image URL and downloads the file.
8. **Merge**: combines post text and image.
9. **Send a photo message → Send message and wait for response**: Telegram image, then text with Approve / Disapprove buttons.
10. **Switch**: Approved → **Update row in sheet** (Status `Created `, post text, image URL). Disapproved → **Limit** → back to **Search**.

## Setup

1. Create the Google Sheet (see `CREDENTIALS.md` for exact headers, including the trailing space in `Topic `).
2. Copy `.env.example` to `.env`, fill it in, and make those variables available to your n8n instance (with `N8N_BLOCK_ENV_ACCESS_IN_NODE=false`).
3. Install the Tavily community node.
4. Import `workflows/linkedin-post-agent.json`.
5. Create the credentials listed in `CREDENTIALS.md` and assign them to the nodes with warnings.
6. Run once with the manual trigger, then activate for the daily schedule.

## Known issues (found during review, not yet fixed in the JSON)

- **The LinkedIn node is empty.** `Create a post` has no person/organization or text configured, so it cannot publish yet. Set it up and map the text to `{{ $('Merge').item.json['Post Text'] }}`.
- **Approval does not reach LinkedIn.** `Update row in sheet` has no outgoing connection, so even once configured, `Create a post` is not triggered (it is also not wired to anything). Connect Switch (Approved) → Update row → Create a post, or similar.
- **Reject loop loses the topic.** Disapproved → Limit → Search runs with Telegram's response as input, so `$json['Topic ']` in the Search query is undefined. Reference `$('Get row(s) in sheet').item.json['Topic ']` in the Search query instead.
- **Unlimited retries.** A reject loop has no cap; add a counter to avoid runaway API costs.
- **Duplicate processing.** The Sheets read returns all `To Do` rows, but downstream nodes assume one. Add a Limit (1) after the read.
- **Hardcoded `2026`** in the Search query.
- The `Wait` node has no duration set; set one (e.g. 20–30 s).
- The sticky notes inside the workflow still mention typing the Kie.ai key into the node; it now uses `KIE_API_KEY`.

## Security

No secrets are in this repository. The original export contained a hardcoded Kie.ai API key; it has been replaced with `$env.KIE_API_KEY`. Rotate that key.

## License

MIT, see `LICENSE`.
