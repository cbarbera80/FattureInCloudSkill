# FattureInCloud Skill for Claude Code

A Claude Code skill for interacting with the **FattureInCloud API v2** — the Italian invoicing platform used by 500k+ businesses. Covers all major domains: issued and received documents, clients, suppliers, products, receipts, F24 tax forms, cashbook entries, electronic invoicing (SDI), webhooks, and more.

---

## Features

| Domain | Operations |
|---|---|
| User & Companies | User info, list companies, company details, plan usage |
| Clients & Suppliers | Create, read, update, delete, search |
| Products | Create, read, update, delete, search |
| Issued Documents | Invoices, quotes, orders, DDT, credit notes — full CRUD + attachments, email, transform, join |
| Electronic Invoicing | XML verify, SDI send, XML download, rejection reason |
| Received Documents | Passive invoices CRUD + pending document queue |
| Receipts (Corrispettivi) | CRUD + monthly totals |
| F24 Tax Forms | CRUD + attachments |
| Archive | Generic document CRUD + attachments |
| Cashbook (Prima Nota) | CRUD entries with date range filter |
| Settings | Payment methods, accounts, VAT types, tax profile, templates |
| Webhooks | Event subscription CRUD + endpoint verification |
| Recycle Bin | Restore or permanently delete soft-deleted documents |

---

## Prerequisites

- **Claude Code** — see installation below
- A FattureInCloud account with API access
- An **OAuth2 Access Token** from the FattureInCloud developer portal

### Getting Your Credentials

1. Go to [developers.fattureincloud.it](https://developers.fattureincloud.it/)
2. Create a new OAuth2 application
3. Complete the authorization flow (Authorization Code Flow or Device Code Flow)
4. Copy your access token and your company ID

---

## Installation

### Option A — Install via `npx skills` (recommended)

The easiest way. Uses the [open agent skills CLI](https://github.com/vercel-labs/skills) to automatically install the skill into the correct directory for Claude Code (and any other AI agent you have installed).

```bash
npx skills add cbarbera80/fattureincloud-skill
```

This clones the skill from GitHub and installs it to `~/.claude/skills/fattureincloud` as a symlink, so you can get updates later with:

```bash
npx skills upgrade cbarbera80/fattureincloud-skill
```

If your system doesn't support symlinks, use `--copy` to install a standalone copy instead:

```bash
npx skills add cbarbera80/fattureincloud-skill --copy
```

---

### Option B — Manual installation

**Step 1 — Install Claude Code** (if not already installed):

```bash
npx @anthropic-ai/claude-code
# or globally:
npm install -g @anthropic-ai/claude-code
```

**Step 2 — Copy the skill:**

```bash
cp -r /path/to/ficskill ~/.claude/skills/fattureincloud
```

Or clone from the repository:

```bash
git clone https://github.com/cbarbera80/fattureincloud-skill.git ~/.claude/skills/fattureincloud
```

---

### Step 3 — Set environment variables

Add these lines to your `~/.zshrc` or `~/.bashrc`:

```bash
export FIC_ACCESS_TOKEN="your_oauth2_access_token"
export FIC_COMPANY_ID="your_company_id"
```

Then reload your shell:

```bash
source ~/.zshrc
```

### Step 4 — Verify the installation

Inside Claude Code, check that the skill is loaded:

```
/skills list
```

You should see `fattureincloud` in the list.

### Step 5 — Quick test

Run this curl command to confirm your credentials are working:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/user/companies" | python3 -m json.tool
```

If you get a list of companies, you're all set.

---

## Usage

Once installed, the skill activates automatically in Claude Code when you make FattureInCloud-related requests. Examples:

```
"Create an invoice for client Mario Rossi for 2 hours of consulting at €100 + 22% VAT"
"List all invoices from January 2024"
"Send invoice n. 5/2024 by email to the client"
"Add a supplier with VAT number IT12345678901"
"Show monthly receipts totals for 2024"
"Send electronic invoice n. 3/2024 to the SDI system"
"What is the current plan usage for this company?"
"List all clients matching 'Rossi'"
```

---

## Skill Structure

```
fattureincloud/
├── SKILL.md                    ← Entry point: auth, conventions, quick reference
├── README.md                   ← This file
└── references/
    ├── user-companies.md       ← User info and companies
    ├── clients-suppliers.md    ← Clients and suppliers
    ├── products.md             ← Product catalog
    ├── issued-documents.md     ← Issued documents (invoices, quotes…)
    ├── e-invoices.md           ← Electronic invoicing / SDI
    ├── received-documents.md   ← Received/passive documents
    ├── receipts.md             ← Receipts (corrispettivi)
    ├── taxes-f24.md            ← F24 tax forms
    ├── archive.md              ← Document archive
    ├── cashbook.md             ← Cashbook (prima nota)
    ├── info.md                 ← Read-only reference data (VAT, payments…)
    ├── settings.md             ← Company settings
    ├── webhooks.md             ← Webhook subscriptions
    └── recycle-bin.md          ← Recycle bin
```

---

## Important Notes

- **Data envelope:** All write requests (POST/PUT) must wrap the body in `{"data": {...}}`. All responses return data wrapped in `"data"` as well.
- **`?type=` is required:** `GET /issued_documents` always requires `?type=invoice` (or another type). Omitting it returns 422.
- **Soft delete vs. permanent delete:** `DELETE /issued_documents/{id}` moves to the recycle bin (reversible). `DELETE /bin/issued_documents/{id}` is **permanent and cannot be undone**.
- **Electronic invoicing:** `POST .../e_invoice/send` is irreversible once accepted by SDI. Always run `xml_verify` first.

---

## API Reference

- Official docs: [developers.fattureincloud.it](https://developers.fattureincloud.it/)
- OpenAPI spec: [github.com/fattureincloud/openapi-fattureincloud](https://github.com/fattureincloud/openapi-fattureincloud)
- Base URL: `https://api-v2.fattureincloud.it`
- API version: 2.1.8
