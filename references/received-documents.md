# Received Documents

Fatture passive e documenti ricevuti da fornitori. All paths are under `/c/$FIC_COMPANY_ID/received_documents`.

---

## Document Types

| Type | Description |
|---|---|
| `expense` | Spesa / fattura passiva |
| `passive_credit_note` | Nota di credito passiva |
| `passive_delivery_note` | DDT passivo |

---

## List Received Documents — `GET /c/{company_id}/received_documents`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/received_documents?type=expense"
```

With filters and pagination:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/received_documents?type=expense&page=1&per_page=50&q=Fornitore"
```

---

## Create Received Document — `POST /c/{company_id}/received_documents`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "type": "expense",
      "entity": {"id": 456},
      "date": "2024-01-10",
      "number": "FT/2024/001",
      "amount_net": 500.00,
      "amount_vat": 110.00,
      "amount_gross": 610.00,
      "vat": {"id": 3},
      "payment_method": {"id": 1},
      "description": "Acquisto materiali",
      "is_marked": false
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/received_documents"
```

> **`entity`** is the supplier object. Use `{"id": N}` with a known supplier ID from `references/clients-suppliers.md`.

---

## Get Received Document — `GET /c/{company_id}/received_documents/{document_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/received_documents/$DOCUMENT_ID"
```

---

## Update Received Document — `PUT /c/{company_id}/received_documents/{document_id}`

```bash
curl -s -X PUT \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "type": "expense",
      "entity": {"id": 456},
      "date": "2024-01-10",
      "number": "FT/2024/001",
      "amount_net": 550.00,
      "amount_vat": 121.00,
      "amount_gross": 671.00,
      "vat": {"id": 3},
      "payment_method": {"id": 1}
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/received_documents/$DOCUMENT_ID"
```

---

## Delete Received Document — `DELETE /c/{company_id}/received_documents/{document_id}`

Moves to recycle bin (soft delete). See `references/recycle-bin.md`.

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/received_documents/$DOCUMENT_ID"
```

---

## Pending Documents (Import Queue)

### List Pending — `GET /c/{company_id}/received_documents/pending`

Documents received via SDI that are waiting to be confirmed and registered:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/received_documents/pending"
```

### Get Pending Document — `GET /c/{company_id}/received_documents/pending/{document_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/received_documents/pending/$DOCUMENT_ID"
```

---

## Compute Totals — `POST /c/{company_id}/received_documents/totals`

Calculate totals for a draft without saving:

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "type": "expense",
      "amount_net": 500.00,
      "vat": {"id": 3}
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/received_documents/totals"
```

---

## Attachment

### Upload Attachment — `POST /c/{company_id}/received_documents/attachment`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -F "attachment=@/path/to/fattura.pdf" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/received_documents/attachment"
```

Returns `{"data": {"attachment_token": "abc123"}}`.

### Delete Attachment — `DELETE /c/{company_id}/received_documents/{document_id}/attachment`

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/received_documents/$DOCUMENT_ID/attachment"
```

---

## Pre-create Info — `GET /c/{company_id}/received_documents/info`

Reference data for building the document creation form:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/received_documents/info?type=expense"
```

---

## Error Reference

| HTTP | Cause | Fix |
|---|---|---|
| `422` | `type` missing | Always include `?type=expense` (or other) |
| `422` | Invalid `vat.id` | Check IDs via `/info/vat_types` |
| `404` | Document not found or in recycle bin | See `references/recycle-bin.md` |
