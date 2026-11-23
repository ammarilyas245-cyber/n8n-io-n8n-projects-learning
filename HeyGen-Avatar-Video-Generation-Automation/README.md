# HeyGen Video Avatar Generation (n8n)

n8n workflows that turn a one-line idea into a short (about 8 second) talking
avatar video. A GPT-4o agent writes the script, then an avatar service renders
the video. The canvas holds three independent flows.

## The three flows

| Flow | Trigger | Steps |
|---|---|---|
| **A. HeyGen status polling** | Form: *Idea* | GPT-4o script -> Edit Fields -> poll HeyGen status -> If -> Wait loop -> append row to Google Sheets |
| **B. HeyGen video generation** | Form: *Idea* | GPT-4o script -> two parallel paths: (1) HeyGen official node `Create video agent` -> Wait -> Get status -> If -> Sheets / retry; (2) raw HTTP `POST /v2/video/generate` with a fixed avatar and voice -> Wait -> status check |
| **C. Kling avatar from your own photo** | Form: *Idea* + *image* upload | Upload image to Google Drive -> make it public -> GPT-4o script -> Kie.ai `kling/ai-avatar-pro` -> Wait -> poll task -> extract video URL -> download video |

## Tech stack

n8n, OpenAI GPT-4o, HeyGen API, Kie.ai (Kling AI Avatar Pro), Google Drive, Google Sheets.

## Repository layout

```
.
├── workflows/heygen-video-avatar-generation.json   # sanitized export
├── .env.example
├── CREDENTIALS.md
├── LICENSE
└── README.md
```

## Setup

1. Read [CREDENTIALS.md](CREDENTIALS.md) and prepare your accounts and keys.
2. `cp .env.example .env` and fill in `HEYGEN_API_KEY`, `KIE_API_KEY`,
   `GOOGLE_DRIVE_FOLDER_ID`; run n8n with `N8N_BLOCK_ENV_ACCESS_IN_NODE=false`.
3. Import `workflows/heygen-video-avatar-generation.json`.
4. Re-select credentials and finish the manual steps listed in CREDENTIALS.md.
5. Open a form trigger's test URL, submit an idea, and check the output.

## Security: keys were exposed in the original export

The original workflow had a HeyGen API key typed directly into four HTTP nodes
and a Kie.ai token into two more. They have been replaced with `$env` references.
**Rotate both keys.** Also removed: a HeyGen key that was being sent in a
request header to kie.ai (a third party that does not need it), pinned test
data, and instance and credential IDs.

## Known issues

- **Edit Fields** sets `video_id` to a hardcoded avatar ID, so flow A polls a
  non-existent video. It should use the `video_id` returned by the generate call.
- **Both If nodes have empty conditions**, so the polling loops don't branch correctly.
- **Share file** made the uploaded image public and discoverable by search
  (`anyone` + `allowFileDiscovery`). It now shares the file just uploaded, but
  consider turning off discovery and deleting the image after rendering.
  Kling needs a URL it can fetch; a public link is the simplest way to do that.
- Flow B's second path (raw HTTP generate) ends at a status check with no output step.
- The audio URL in flow C is a sample mp3, not your script's voice.
- The raw HTTP body inserts the script unquoted; JSON-escape it if scripts contain quotes.

## License

MIT, see [LICENSE](LICENSE). Replace `<YOUR NAME>` with your name.
