# Required Credentials & Configuration

Everything you must set up before the workflow in
`workflows/gmail-customer-query-automation.json` will run.

> The exported workflow contains **no API keys or secrets**. Credential
> references were stripped to names only, so after importing you must
> re-select each credential on its nodes.

## 1. n8n Credentials (create under *Credentials* in n8n)

| Credential name (as used in workflow) | n8n credential type | Used by | How to get it |
|---|---|---|---|
| `Gmail account` | Gmail OAuth2 API | Gmail Trigger + 30+ Gmail nodes (reply, label, send) | Google Cloud Console: enable Gmail API, create OAuth client, add scopes for read/send/modify |
| `Google Sheets account` | Google Sheets OAuth2 API | Inventory lookup, order append/search/delete | Same Google Cloud project: enable Google Sheets API |
| `OpenAI account` | OpenAI API | All chat models, Message a model nodes, embeddings (gpt-4o, gpt-4o-mini, gpt-4.1, gpt-4.1-mini, gpt-5, gpt-5-mini, embeddings) | https://platform.openai.com/api-keys |
| `Pinecone account` | Pinecone API | Pinecone Vector Store (Customer Support branch) | https://app.pinecone.io -> API Keys |
| `NVIDIA Nemotron account` | NVIDIA API | NVIDIA Nemotron Chat Model (Email Classifier) | https://build.nvidia.com |

Note: the Email Classifier is wired to the NVIDIA model. The OpenAI Chat
Model nodes are separate and do not replace it.

## 2. Environment variables (see `.env.example`)

| Variable | Purpose |
|---|---|
| `N8N_BLOCK_ENV_ACCESS_IN_NODE` | Must be `false` so `$env.*` works inside nodes |
| `INVENTORY_SHEET_ID` | Google Sheet with stock levels (SKU, Category, Product Name, Color, Size, Unit Price (PKR), Availability, Warehouse Location) |
| `ORDERS_SHEET_ID` | Google Sheet used as the order log (columns listed below) |
| `STAFF_COMPLAINT_TEAM_EMAIL` | Receives staff-complaint alerts |
| `HR_TEAM_EMAIL` | Receives employee-complaint alerts |
| `LEGAL_TEAM_EMAIL` | Receives government/legal notices |
| `FULFILLMENT_TEAM_EMAIL` | Receives order-cancellation notices |

## 3. Google Sheets structure

**Inventory sheet (`INVENTORY_SHEET_ID`)**: SKU, Category, Product Name,
Color, Size, Unit Price (PKR), Stock Quantity, Availability
(`In Stock` / `Low Stock` / `Out of Stock`), Warehouse Location
(`Lahore DC` / `Karachi DC` / `Islamabad DC`).

**Orders sheet (`ORDERS_SHEET_ID`)**: Date Of Order, Order ID, Customer Name,
Email Address, Shipping Address, Contact, SKU, Category, Product Name, Color,
Size, Unit Price (PKR), QTY, Total Amount (PKR), Availability, Warehouse
Location, `Delivery date ` (note the trailing space), Delivery Status.

## 4. Gmail labels

Create these 8 labels in Gmail, then re-select them on the matching
"Add label to message" nodes (label IDs are account-specific):

Customer Support, New Order, Return, Order Status, Complaint, Payment Issue,
Order Cancellation, Review & Feedback.

## 5. Pinecone

- Index named `maison-luxe-records`, namespace `Brand Information`
- Populated with embeddings (OpenAI) of your brand documents: products,
  store hours/locations, policies, returns, appointments.
- Embedding model must match the one used when the index was built.

## 6. Security notes

- Never commit `.env` or any exported workflow that still has credential IDs.
- Before re-exporting from n8n, re-run the sanitizing steps (strip
  `credentials.*.id`, sheet IDs, emails, `meta.instanceId`).
- If this repository was ever public with the original export, consider
  rotating the Google OAuth client secret as a precaution.
