# Receipts (Corrispettivi)

Gestione dei corrispettivi (scontrini fiscali, registrazioni di cassa). All paths are under `/c/$FIC_COMPANY_ID/receipts`.

---

## List Receipts — `GET /c/{company_id}/receipts`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/receipts"
```

With date range and pagination:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/receipts?page=1&per_page=50"
```

---

## Create Receipt — `POST /c/{company_id}/receipts`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "date": "2024-01-15",
      "number": 1,
      "description": "Vendita al dettaglio",
      "rc_center": "Negozio principale",
      "payment_type": "cash",
      "items_list": [
        {
          "description": "Prodotto A",
          "amount_net": 20.00,
          "amount_gross": 24.40,
          "vat": {"id": 3}
        },
        {
          "description": "Prodotto B",
          "amount_net": 10.00,
          "amount_gross": 11.00,
          "vat": {"id": 4}
        }
      ]
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/receipts"
```

**Payment type values:**

| Value | Description |
|---|---|
| `cash` | Contanti |
| `credit_card` | Carta di credito/debito |
| `bank_transfer` | Bonifico |
| `other` | Altro |

---

## Get Receipt — `GET /c/{company_id}/receipts/{document_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/receipts/$DOCUMENT_ID"
```

---

## Update Receipt — `PUT /c/{company_id}/receipts/{document_id}`

```bash
curl -s -X PUT \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "date": "2024-01-15",
      "number": 1,
      "description": "Vendita aggiornata",
      "payment_type": "credit_card",
      "items_list": [
        {
          "description": "Prodotto A aggiornato",
          "amount_net": 22.00,
          "amount_gross": 26.84,
          "vat": {"id": 3}
        }
      ]
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/receipts/$DOCUMENT_ID"
```

---

## Delete Receipt — `DELETE /c/{company_id}/receipts/{document_id}`

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/receipts/$DOCUMENT_ID"
```

---

## Pre-create Info — `GET /c/{company_id}/receipts/info`

Returns available payment types and category options for form building:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/receipts/info"
```

---

## Monthly Totals — `GET /c/{company_id}/receipts/monthly_totals`

Returns aggregated receipt totals grouped by month for a given year:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/receipts/monthly_totals?year=2024"
```

Example response:

```json
{
  "data": [
    {"month": 1, "amount_net": 1200.00, "amount_gross": 1464.00},
    {"month": 2, "amount_net": 980.00,  "amount_gross": 1195.60}
  ]
}
```

---

## Error Reference

| HTTP | Cause | Fix |
|---|---|---|
| `422` | Invalid `vat.id` | Check via `/info/vat_types` |
| `422` | Missing `date` or `items_list` | Both are required |
| `404` | Receipt not found | Verify `$DOCUMENT_ID` |
