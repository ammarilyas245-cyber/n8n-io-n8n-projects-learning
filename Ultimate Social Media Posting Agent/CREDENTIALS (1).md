# Required Credentials

Create each credential in n8n (**Credentials → Create Credential**), then open the
listed nodes and select it. The exported workflow contains only credential *names*,
never secrets.

| # | Credential type (n8n) | Suggested name | Env variable(s) in `.env` | Where to get it | Used by nodes |
|---|---|---|---|---|---|
| 1 | OpenAI API | `OpenAI account` | `OPENAI_API_KEY` | https://platform.openai.com/api-keys | OpenAI Chat Model (`gpt-4o`), Generate an image (`gpt-image-1-mini`) |
| 2 | Tavily API | `Tavily account` | `TAVILY_API_KEY` | https://app.tavily.com | Search in Tavily |
| 3 | LinkedIn OAuth2 API | `LinkedIn OAuth Connection` | `LINKEDIN_CLIENT_ID`, `LINKEDIN_CLIENT_SECRET` | https://www.linkedin.com/developers/apps (needs the *Share on LinkedIn* product) | Create a post |
| 4 | Google Drive OAuth2 API | `Google Drive account` | `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET` | Google Cloud Console → APIs & Services → Credentials (enable Google Drive API) | Upload file, Share file |
| 5 | Instagram API (community node) | `Instagram account` | `INSTAGRAM_ACCESS_TOKEN` | Meta for Developers → Instagram Graph API (Business or Creator account linked to a Facebook Page) | Post on Instagram |
| 6 | Facebook Graph API | `Facebook Graph account` | `FACEBOOK_APP_ID`, `FACEBOOK_APP_SECRET`, `FACEBOOK_PAGE_ACCESS_TOKEN` | https://developers.facebook.com (Graph API Explorer or your app) | Facebook Graph API |

## Placeholders to replace inside the workflow JSON

| Placeholder | Meaning | Env variable |
|---|---|---|
| `YOUR_LINKEDIN_PERSON_ID` | Author ID in the *Create a post* node (`person` field) | `LINKEDIN_PERSON_ID` |
| `YOUR_INSTAGRAM_BUSINESS_ACCOUNT_ID` | Instagram Business Account ID in *Post on Instagram* (also mentioned in a sticky note) | `INSTAGRAM_BUSINESS_ACCOUNT_ID` |

The *Facebook Graph API* node targets `me`. With a **Page access token** this resolves to
the Page; if you prefer to be explicit, replace `me` with your Page ID (`FACEBOOK_PAGE_ID`).

## Other requirements

- **Community nodes:** `@tavily/n8n-nodes-tavily` and `@mookielianhd/n8n-nodes-instagram` (Settings → Community Nodes).
- **Permissions:** LinkedIn needs the `w_member_social` scope; Facebook Page tokens need `pages_manage_posts` and related Page permissions; Instagram publishing needs `instagram_content_publish`. Meta app review may be required for production use.
- **Paid usage:** OpenAI text and image calls and Tavily searches consume credits.
- Google and Meta OAuth apps in development/testing mode only work for accounts added as testers.
