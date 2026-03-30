# Issued Documents

Fatture emesse, preventivi, ordini, note di credito e altri documenti attivi. All paths are under `/c/$FIC_COMPANY_ID/issued_documents`.

---

## Document Types

The `type` parameter identifies the document kind:

| Type | Description |
|---|---|
| `invoice` | Fattura |
| `quote` | Preventivo |
| `credit_note` | Nota di credito |
| `order` | Ordine cliente |
| `work_report` | Rapporto di lavoro |
| `supplier_order` | Ordine a fornitore |
| `delivery_note` | Documento di trasporto (DDT) |
| `passive_credit_note` | Nota di credito passiva |
| `self_own_invoice` | Autofattura |
| `self_supplier_invoice` | Fattura da fornitore (reverse charge) |

---

## List Issued Documents — `GET /c/{company_id}/issued_documents`

> **The `type` parameter is required.** Omitting it returns a 422 error.

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents?type=invoice"
```

With filters and pagination:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents?type=invoice&page=1&per_page=50&q=Rossi"
```

---

## Create Issued Document — `POST /c/{company_id}/issued_documents`

Minimal example for an invoice:

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "type": "invoice",
      "number": 1,
      "date": "2024-01-15",
      "entity": {"id": 123},
      "items_list": [
        {
          "product_id": 456,
          "name": "Consulenza",
          "qty": 2,
          "measure": "ore",
          "net_price": 100.00,
          "vat": {"id": 3},
          "discount": 0
        }
      ],
      "payment_method": {"id": 1},
      "notes": "Pagamento a 30 giorni",
      "currency": {"id": "EUR", "exchange_rate": "1.00000"}
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents"
```

> **`entity`** is the client object; use `{"id": N}` with a known client ID, or embed the full client object inline.
> **`vat`** references a VAT type by ID (from `GET /info/vat_types`).
> **`payment_method`** references a payment method by ID (from `GET /info/payment_methods`).

---

## Get Issued Document — `GET /c/{company_id}/issued_documents/{document_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID"
```

---

## Update Issued Document — `PUT /c/{company_id}/issued_documents/{document_id}`

Must send the **full document object** (partial updates are not supported):

```bash
curl -s -X PUT \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "type": "invoice",
      "number": 1,
      "date": "2024-01-15",
      "entity": {"id": 123},
      "items_list": [
        {
          "name": "Consulenza aggiornata",
          "qty": 3,
          "net_price": 100.00,
          "vat": {"id": 3}
        }
      ],
      "payment_method": {"id": 1}
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID"
```

---

## Delete Issued Document — `DELETE /c/{company_id}/issued_documents/{document_id}`

Moves the document to the recycle bin (soft delete, recoverable). See `references/recycle-bin.md`.

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID"
```

---

## Compute Totals (New Document) — `POST /c/{company_id}/issued_documents/totals`

Calculate totals for a draft without saving:

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "type": "invoice",
      "entity": {"id": 123},
      "items_list": [
        {"qty": 2, "net_price": 100.00, "vat": {"id": 3}}
      ]
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/totals"
```

## Compute Totals (Existing Document) — `POST /c/{company_id}/issued_documents/{document_id}/totals`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"data": {}}' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID/totals"
```

---

## Attachment

### Upload Attachment — `POST /c/{company_id}/issued_documents/attachment`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -F "attachment=@/path/to/allegato.pdf" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/attachment"
```

Returns an `attachment_token` to use when creating/updating a document:

```json
{"data": {"attachment_token": "abc123token"}}
```

### Delete Attachment — `DELETE /c/{company_id}/issued_documents/{document_id}/attachment`

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID/attachment"
```

---

## Email

### Get Email Prefill Data — `GET /c/{company_id}/issued_documents/{document_id}/email`

Returns pre-filled subject, body, and recipient for sending the document by email.

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID/email"
```

### Send Document by Email — `POST /c/{company_id}/issued_documents/{document_id}/email`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "recipient_email": "cliente@example.com",
      "subject": "Fattura n. 1/2024",
      "body": "In allegato la fattura. Cordiali saluti.",
      "include_pdf": true,
      "cc_email": ""
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID/email"
```

---

## Pre-create Info — `GET /c/{company_id}/issued_documents/info`

Returns reference data for building the document creation form (next number, default values, etc.).

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/info?type=invoice"
```

---

## Transform Document — `GET /c/{company_id}/issued_documents/transform`

Convert an existing document to a different type (e.g. quote → invoice):

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/transform?original_document_id=$DOCUMENT_ID&new_type=invoice"
```

Returns the new document prefilled (not yet saved). Save it with a subsequent `POST /issued_documents`.

---

## Join Documents — `GET /c/{company_id}/issued_documents/join`

Merge multiple documents of the same type into one:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/join?ids[]=101&ids[]=102&ids[]=103&type=invoice"
```

Returns the merged document prefilled (not yet saved).

---

## Error Reference

| HTTP | Cause | Fix |
|---|---|---|
| `422` | `type` missing from list request | Always include `?type=invoice` (or other type) |
| `422` | Invalid `vat.id` or `payment_method.id` | Check IDs via `/info/vat_types` and `/info/payment_methods` |
| `422` | Missing required `entity` or `items_list` | Include at least one client and one line item |
| `404` | Document not found | May be in recycle bin; see `references/recycle-bin.md` |
