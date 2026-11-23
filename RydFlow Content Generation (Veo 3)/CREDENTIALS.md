# Required Credentials

Create each credential in n8n (**Credentials → Create Credential**), then open the
listed nodes and select it. The exported workflow contains only credential *names*,
never secrets.

| # | Credential type (n8n) | Suggested name | Env variable(s) in `.env` | Where to get it | Used by nodes |
|---|---|---|---|---|---|
| 1 | OpenAI API | `OpenAI account` | `OPENAI_API_KEY` | https://platform.openai.com/api-keys | OpenAI Chat Model, Model1, Model2, Model3 (all `gpt-4.1`) |
| 2 | Header Auth (HTTP) | `Kie API` | `KIE_API_KEY` | https://kie.ai (API key page) | Image 1-4 Generation, Get Image 1-4, Video Generation 1-4, Get Video 1-4 (16 nodes) |
| 3 | Cloudinary API | `Cloudinary account` | `CLOUDINARY_CLOUD_NAME`, `CLOUDINARY_API_KEY`, `CLOUDINARY_API_SECRET` | https://console.cloudinary.com → Settings → API Keys | Upload an asset from file data (x4) |
| 4 | Google Sheets OAuth2 API | `Google Sheets account` | `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET` | Google Cloud Console → APIs & Services → Credentials (enable Google Sheets API) | Get Previous Scripts, Logging x3, Update ImageURL x4, Update VideoURL x4 (12 nodes) |

### Kie API header credential

In n8n choose **Header Auth** and set:

| Field | Value |
|---|---|
| Name | `Authorization` |
| Value | `Bearer <your Kie.ai API key>` |

## Placeholders to replace inside the workflow JSON

| Placeholder | Meaning | Env variable |
|---|---|---|
| `YOUR_GOOGLE_SHEET_ID` | Your Google Sheet (appears in the 12 Google Sheets nodes; re-pick the document from the node's list after connecting Google) | `GOOGLE_SHEET_ID` |
| `YOUR_CLOUD_NAME` | Your Cloudinary cloud name, used in the 7 character-reference image URLs inside each *Image N Generation* node (4 nodes) | `CLOUDINARY_CLOUD_NAME` |

## Character reference images

The image nodes expect 7 reference photos uploaded to your Cloudinary account:
`Maria_Owner.jpg`, `Chloe_Wedding_Planner.jpg`, `DeShawn_Dispatcher.jpg`,
`Ms._Reed_School_Transport_Director.jpg`, `Rosa_Senior_Driver.jpg`,
`Sam_Affiliate_Partner.jpg`, `Dr._Lee_Clinic_Coordinator.jpg`.
They are not included in this repo. Upload your own and adjust the URLs if your paths differ.

## Other requirements

- **Community node:** `n8n-nodes-cloudinary` (Settings → Community Nodes).
- **Paid usage:** Kie.ai image and video jobs and OpenAI calls consume credits.
- Google OAuth apps in "Testing" mode need your account added as a test user.
