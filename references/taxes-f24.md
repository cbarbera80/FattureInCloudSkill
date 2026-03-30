# Taxes / F24

Gestione dei versamenti F24 (pagamenti delle imposte). All paths are under `/c/$FIC_COMPANY_ID/taxes`.

---

## List F24 — `GET /c/{company_id}/taxes`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/taxes"
```

With pagination:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/taxes?page=1&per_page=50"
```

---

## Create F24 — `POST /c/{company_id}/taxes`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "date": "2024-06-17",
      "description": "Acconto IRPEF giugno 2024",
      "amount": 1500.00,
      "status": "due",
      "payment_account": {"id": 1}
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/taxes"
```

**Status values:**

| Value | Description |
|---|---|
| `due` | Da pagare |
| `paid` | Pagato |
| `not_due` | Non dovuto |

---

## Get F24 — `GET /c/{company_id}/taxes/{f24_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/taxes/$F24_ID"
```

---

## Update F24 — `PUT /c/{company_id}/taxes/{f24_id}`

```bash
curl -s -X PUT \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "date": "2024-06-17",
      "description": "Acconto IRPEF giugno 2024",
      "amount": 1500.00,
      "status": "paid",
      "payment_account": {"id": 1}
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/taxes/$F24_ID"
```

---

## Delete F24 — `DELETE /c/{company_id}/taxes/{f24_id}`

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/taxes/$F24_ID"
```

---

## Attachment

### Upload Attachment — `POST /c/{company_id}/taxes/attachment`

Attach the PDF copy of the F24 form:

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -F "attachment=@/path/to/f24.pdf" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/taxes/attachment"
```

Returns `{"data": {"attachment_token": "abc123"}}`. Include the token in the F24 `data` when creating/updating.

### Delete Attachment — `DELETE /c/{company_id}/taxes/{f24_id}/attachment`

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/taxes/$F24_ID/attachment"
```

---

## Key Fields Reference

| Field | Description |
|---|---|
| `date` | Data di scadenza del versamento (YYYY-MM-DD) |
| `description` | Descrizione del versamento |
| `amount` | Importo totale |
| `status` | `due`, `paid`, o `not_due` |
| `payment_account` | Conto di pagamento `{"id": N}` (from `/info/payment_accounts`) |
| `attachment_token` | Token allegato PDF (from upload endpoint) |

---

## Error Reference

| HTTP | Cause | Fix |
|---|---|---|
| `422` | Invalid `payment_account.id` | Verify via `/info/payment_accounts` |
| `422` | Missing `amount` or `date` | Both are required |
| `404` | F24 ID not found | Verify `$F24_ID` |
