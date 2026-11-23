# Required Credentials

This workflow needs the credentials below. Create each one in n8n
(**Credentials → Create Credential**), then open the listed nodes and select it.
The exported workflow contains only credential *names*, never secrets.

| # | Credential type (n8n) | Suggested name | Env variable(s) in `.env` | Where to get it | Used by nodes |
|---|---|---|---|---|---|
| 1 | OpenAI API | `OpenAI account` | `OPENAI_API_KEY` | https://platform.openai.com/api-keys | OpenAI Chat Model (0–4), Embeddings OpenAI (0–2), Message a model, Message a model2 |
| 2 | Groq API | `Groq account` | `GROQ_API_KEY` | https://console.groq.com/keys | Groq Chat Model (Welcome + T&C agents) |
| 3 | OpenRouter API | `OpenRouter account` | `OPENROUTER_API_KEY` | https://openrouter.ai/keys | OpenRouter Chat Model, OpenRouter Chat Model1 |
| 4 | SerpApi | `SerpApi account` | `SERPAPI_API_KEY` | https://serpapi.com/manage-api-key | Google search in SerpApi, Google search in SerpApi1 |
| 5 | OpenWeatherMap API | `OpenWeatherMap account` | `OPENWEATHERMAP_API_KEY` | https://home.openweathermap.org/api_keys | OpenWeatherMap |
| 6 | Pinecone API | `Pinecone account` | `PINECONE_API_KEY`, `PINECONE_INDEX_NAME` | https://app.pinecone.io | Pinecone Vector Store (0–4) |
| 7 | Gmail OAuth2 API | `Gmail OAuth2 API` | `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET` | Google Cloud Console → APIs & Services → Credentials (enable Gmail API) | Send a message in Gmail, Send a message in Gmail1, Send a message, Send a message1 |
| 8 | Google Drive OAuth2 API | `Google Drive account` | `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `GOOGLE_DRIVE_FILE_ID` | Same Google Cloud project (enable Drive API) | Download file, Download file1 |
| 9 | Slack API | `Slack account` | `SLACK_BOT_TOKEN`, `SLACK_SIGNING_SECRET`, `SLACK_CHANNEL_ID` | https://api.slack.com/apps | Slack Trigger, Send a message2 |

## Placeholders to replace inside the workflow JSON

| Placeholder | Meaning | Env variable |
|---|---|---|
| `your-email@example.com` | Address the Gmail tool nodes send to | `NOTIFICATION_EMAIL` |
| `sender@example.com` | Sign-off address in onboarding email prompts and code | `SENDER_EMAIL` |
| `client@example.com` | Placeholder text in the onboarding form (cosmetic) | n/a |
| `YOUR_SLACK_CHANNEL_ID` | Slack channel for the trigger and reply | `SLACK_CHANNEL_ID` |
| `YOUR_GOOGLE_DRIVE_FILE_ID` | The recipes PDF in Google Drive | `GOOGLE_DRIVE_FILE_ID` |
| `YOUR_WEATHER_CHILD_WORKFLOW_ID` | ID of the separate Weather workflow | `WEATHER_CHILD_WORKFLOW_ID` |

## Notes

- The **Weather** tool nodes call a separate *child workflow*. It was not part of
  the export, so you must build or import it yourself and paste its ID.
- The `n8n-nodes-serpapi` community node must be installed (Settings → Community Nodes).
- The Pinecone index must use **1536 dimensions** (matches the OpenAI embeddings node).
- Several OpenAI nodes use `gpt-4o-mini`; the RAG chain uses `gpt-5-mini`.
- Google OAuth apps in "Testing" mode need your account added as a test user.
