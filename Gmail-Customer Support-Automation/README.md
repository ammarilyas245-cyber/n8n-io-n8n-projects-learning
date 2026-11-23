# Maison Luxe - Gmail Customer Query Automation

An [n8n](https://n8n.io) workflow that reads every incoming customer email,
classifies it with AI, and sends the right reply automatically: answering
questions from a knowledge base, checking stock, logging orders, handling
returns, cancellations and payment disputes, and routing complaints to the
right internal team.

Built for **Maison Luxe**, a premium men's fashion retailer in Pakistan.

## How it works

`Gmail Trigger` (polls every minute) -> `Email Classifier` -> one of 8 branches:

| # | Category | What happens |
|---|---|---|
| 1 | **Customer Support** | AI agent searches a Pinecone knowledge base (products, stores, policies) and replies in-thread |
| 2 | **New Order** | Extracts order details, looks up SKU/price/stock in Google Sheets, calculates totals with a Calculator tool, splits by warehouse city, generates Order IDs, appends to the Orders sheet, sends an In Stock / Low Stock confirmation or an Out of Stock apology |
| 3 | **Return Order** | Extracts the request, finds the order, applies policy rules (non-returnable categories, 7-day window), then sends an Exchange, Refund or Denial email |
| 4 | **Order Status** | Finds the order, computes On Track / Delayed / Delivered, sends a status-specific reply |
| 5 | **Complaints** | Classifies Staff / Employee / Government. Staff and employee complaints get an acknowledgment plus an internal alert; **government notices get no auto-reply** and are forwarded to the legal team |
| 6 | **Payment Issues** | Compares the customer's claim to the order record and replies for refund, wrong-amount or failed-transaction cases |
| 7 | **Cancellations** | Finds the order, deletes its row, confirms to the customer and notifies the team |
| 8 | **Feedback / Reviews** | Sentiment analysis, then a tone-matched reply |

Unclassifiable emails go to a no-op node.

## Tech stack

n8n, Gmail API, Google Sheets, OpenAI (GPT-4o / 4.1 / 5 families), Pinecone,
NVIDIA Nemotron (classifier).

## Repository layout

```
.
├── workflows/
│   └── gmail-customer-query-automation.json   # sanitized n8n export
├── .env.example                               # env vars template
├── CREDENTIALS.md                             # full credential checklist
├── LICENSE
└── README.md
```

## Setup

1. **Prepare accounts**: follow [CREDENTIALS.md](CREDENTIALS.md) (Google
   OAuth, OpenAI, Pinecone, NVIDIA).
2. **Configure environment**:
   ```bash
   cp .env.example .env
   # fill in sheet IDs and team emails
   ```
   Make sure n8n runs with `N8N_BLOCK_ENV_ACCESS_IN_NODE=false`.
3. **Create the Google Sheets** (inventory + orders) with the columns listed
   in CREDENTIALS.md, and the 8 Gmail labels.
4. **Load your Pinecone index** `maison-luxe-records` (namespace
   `Brand Information`) with your brand documents.
5. **Import** `workflows/gmail-customer-query-automation.json` in n8n
   (*Workflows -> Import from File*).
6. **Re-select credentials** on each node and **re-pick the Gmail labels**
   on every "Add label to message" node.
7. **Test with a few sample emails before activating.** The workflow replies
   to real customers, and the cancellation branch permanently deletes sheet rows.

## Known issues worth fixing

- `Reply to a message15` references a node called `Transection Issue Model`,
  but the node is named `Transaction Issue Model`, so the failed-transaction
  reply will error until renamed.
- `Reply to a message7` contains a malformed support address
  (`@support_maisonluxe.com`); the other branches use `support@maisonluxe.pk`.
- The `Delete rows or columns from sheet` node has no row-matching
  configured; set it to delete the specific matched row, not arbitrary rows.
- `Sentiment Analysis` and the Denied/Exchange/Refund paths should be tested
  end to end; some replies depend on structured output formats.
- Customer-facing replies are sent with no human review. Consider adding an
  approval step for refunds, cancellations and complaints.

## Security

No secrets are stored in this repo. Credentials live in n8n's encrypted store;
sheet IDs and internal emails come from environment variables. See
[CREDENTIALS.md](CREDENTIALS.md).

## License

MIT, see [LICENSE](LICENSE). Replace `<YOUR NAME>` with your name.
