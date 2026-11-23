# Invoice Parser Agent (n8n)

An n8n workflow that watches a Google Drive folder for new invoice PDFs, uses AI
to extract the key details, logs them in a Google Sheet, and emails your billing
team a notification, all within about a minute of the file landing.

## How it works

```
Google Drive Trigger (new file in folder, polls every minute)
   -> Download file
   -> Extract from File (PDF -> text)
   -> Information Extractor (NVIDIA Nemotron)
   -> Append row in sheet (Google Sheets)
   -> Email Expert (GPT-4o writes subject + body as JSON)
   -> Send a message (Gmail)
```

Extracted fields: Invoice Number, Sender Name, Sender Email, Sender Address,
Total Amount, Invoice Date, Due Date.

## Tech stack

n8n, Google Drive, Google Sheets, Gmail, NVIDIA Nemotron
(`nvidia/llama-3.3-nemotron-super-49b-v1.5`) for extraction, OpenAI `gpt-4o` for the email.

## Repository layout

```
.
├── workflows/invoice-parser-agent.json   # sanitized export
├── .env.example
├── CREDENTIALS.md
├── LICENSE
└── README.md
```

## Setup

1. Prepare accounts per [CREDENTIALS.md](CREDENTIALS.md).
2. Create a Drive folder for invoices and a Google Sheet with these exact
   headers (note the trailing spaces): `Invoice Number `, `Sender Name`,
   `Sender Email`, `Sender Address `, `Total Amount `, `Invoice Date`, `Due Date `.
3. `cp .env.example .env`, fill in the folder ID, sheet ID and billing email, and
   run n8n with `N8N_BLOCK_ENV_ACCESS_IN_NODE=false`.
4. Import `workflows/invoice-parser-agent.json` and re-select credentials.
5. Drop a sample invoice PDF in the folder and confirm a row appears and the email arrives.

## Known limitations and suggestions

- **PDFs only.** The trigger fires on every file type, but only PDFs can be read.
  A non-PDF file will error. Filter by MIME type or add an If node.
- **Scanned invoices** (images inside a PDF) have no text layer and will extract
  nothing. Add OCR for those.
- **No duplicate check.** Re-uploading a file creates a second row and email.
- **No validation of extracted values**, and the amount is stored as text. Check
  totals and dates before relying on the sheet for accounting.
- **Fields are all "required"** in the extractor, but missing values are still allowed to pass.
- The email prompt has typos ("compare" should be "compose", "reciept"); the
  model copes, but fix them.
- **Privacy:** invoices contain financial and personal data sent to NVIDIA and
  OpenAI. Don't commit sample invoices (PDFs are ignored in `.gitignore`).

## Security

No secrets are stored in this repo. Credentials live in n8n's encrypted store; the
Drive folder ID, sheet ID and billing email come from environment variables.

## License

MIT, see [LICENSE](LICENSE). Replace `<YOUR NAME>` with your name.
