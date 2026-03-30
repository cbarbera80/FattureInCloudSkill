---
name: fattureincloud
description: Interact with FattureInCloud API v2 for Italian invoicing, clients, suppliers, products, receipts, taxes (F24), cashbook, and e-invoicing (fatturazione elettronica). Use this skill for any FattureInCloud operation.
version: 1.0.0
---

# Instructions

You are an expert in managing Italian business accounting and invoicing workflows using the **FattureInCloud REST API v2**. You help users interact with FattureInCloud programmatically: creating and managing invoices, clients, suppliers, products, receipts, F24 tax forms, cashbook entries, electronic invoices (fatturazione elettronica via SDI), and more.

All requests use `curl`. Always use the environment variables defined below — never hardcode credentials or IDs.

---

## Authentication

Read the access token from `$FIC_ACCESS_TOKEN`. If not set, instruct the user to export it before running any command:

```bash
export FIC_ACCESS_TOKEN=your_oauth2_bearer_token_here
```

Use it in every request as a Bearer token:

```bash
-H "Authorization: Bearer $FIC_ACCESS_TOKEN"
```

The token is obtained via FattureInCloud OAuth2 (Authorization Code Flow or Device Code Flow). If the user needs to generate a token, refer them to https://developers.fattureincloud.it/docs/authentication/.

---

## Company ID

Every company-scoped endpoint requires a company ID in the URL path. Read it from `$FIC_COMPANY_ID`:

```bash
export FIC_COMPANY_ID=12345
```

Use it in every company-scoped URL:

```
/c/$FIC_COMPANY_ID/...
```

To find available company IDs for the authenticated user:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/user/companies"
```

---

## Base URL

```
https://api-v2.fattureincloud.it
```

All paths below are relative to this base URL.

---

## Quick Reference

Use this decision tree to find the right reference file:

| What do you need? | Reference file |
|---|---|
| User info / list companies | `references/user-companies.md` |
| Manage clients or suppliers | `references/clients-suppliers.md` |
| Manage products / catalog | `references/products.md` |
| Issued documents (invoices, quotes, orders, credit notes…) | `references/issued-documents.md` |
| Electronic invoicing / fatturazione elettronica (SDI) | `references/e-invoices.md` |
| Received documents / fatture passive | `references/received-documents.md` |
| Receipts / corrispettivi | `references/receipts.md` |
| F24 tax payments | `references/taxes-f24.md` |
| Archive documents | `references/archive.md` |
| Cashbook / prima nota | `references/cashbook.md` |
| Reference data (VAT types, payment methods, accounts) | `references/info.md` |
| Settings (payment methods, VAT types, templates) | `references/settings.md` |
| Webhooks / event subscriptions | `references/webhooks.md` |
| Recycle bin (restore or permanently delete documents) | `references/recycle-bin.md` |

---

## Data Envelope

**Critical:** All write requests (POST, PUT) must wrap the body in a `"data"` key:

```json
{"data": { ... your fields here ... }}
```

All successful responses also return data wrapped in `"data"`:

```json
{"data": { ... response fields ... }}
```

List responses include pagination metadata alongside the `"data"` array:

```json
{
  "data": [ ... ],
  "current_page": 1,
  "last_page": 4,
  "per_page": 50,
  "total_count": 180
}
```

---

## Common Patterns

### Pagination

All list endpoints support `?page=N&per_page=M`. Default `per_page` is typically 50. Loop until `current_page == last_page`:

```bash
# Fetch page 1
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/clients?page=1&per_page=50"

# Fetch page 2
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/clients?page=2&per_page=50"
```

### Text Search

Most list endpoints accept `?q=<text>` for full-text search:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/clients?q=Rossi"
```

### Fieldset Reduction

Use `?fields=id,name` to reduce payload size on any GET list:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/products?fields=id,code,name,net_price"
```

### Pretty-Printing JSON

Append `| python3 -m json.tool` or `| jq '.'` to any curl command for readable output:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/user/info" | python3 -m json.tool
```

### Multipart Attachment Upload

Use `-F` for file uploads:

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -F "attachment=@/path/to/document.pdf" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/attachment"
```

---

## Error Handling

| HTTP | Meaning | Action |
|---|---|---|
| `401` | Token missing, expired, or invalid | Check `$FIC_ACCESS_TOKEN`; re-authorize via OAuth2 |
| `403` | Insufficient scope or plan limit | Verify token scopes in your OAuth2 app; check plan usage |
| `404` | Resource not found | Verify IDs; resource may be in the recycle bin |
| `422` | Validation error | Read `data.error.validation_result` in the response body |
| `429` | Rate limit exceeded | Back off and retry with exponential backoff |

Check status codes with `-w "\n%{http_code}"`:

```bash
curl -s -w "\n%{http_code}" \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/user/info"
```

---

## Resources

- [`references/user-companies.md`](references/user-companies.md) — User info and company management (4 endpoints)
- [`references/clients-suppliers.md`](references/clients-suppliers.md) — Clients and suppliers CRUD (11 endpoints)
- [`references/products.md`](references/products.md) — Product catalog CRUD (5 endpoints)
- [`references/issued-documents.md`](references/issued-documents.md) — Invoices, quotes, orders, credit notes (12 endpoints)
- [`references/e-invoices.md`](references/e-invoices.md) — Electronic invoicing / SDI (4 endpoints)
- [`references/received-documents.md`](references/received-documents.md) — Received/passive documents (9 endpoints)
- [`references/receipts.md`](references/receipts.md) — Receipts / corrispettivi (7 endpoints)
- [`references/taxes-f24.md`](references/taxes-f24.md) — F24 tax forms (7 endpoints)
- [`references/archive.md`](references/archive.md) — Document archive (6 endpoints)
- [`references/cashbook.md`](references/cashbook.md) — Cashbook / prima nota (5 endpoints)
- [`references/info.md`](references/info.md) — Read-only reference data (8 endpoints)
- [`references/settings.md`](references/settings.md) — Settings CRUD (16 endpoints)
- [`references/webhooks.md`](references/webhooks.md) — Webhook subscriptions (6 endpoints)
- [`references/recycle-bin.md`](references/recycle-bin.md) — Recycle bin management (8 endpoints)
