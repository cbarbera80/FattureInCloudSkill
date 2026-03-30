# Cashbook (Prima Nota)

Gestione della prima nota / libro cassa. All paths are under `/c/$FIC_COMPANY_ID/cashbook`.

---

## List Cashbook Entries — `GET /c/{company_id}/cashbook`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/cashbook"
```

Filter by date range:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/cashbook?date_from=2024-01-01&date_to=2024-01-31&page=1&per_page=50"
```

---

## Create Cashbook Entry — `POST /c/{company_id}/cashbook`

**Incoming payment (entrata):**

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "date": "2024-01-15",
      "description": "Incasso fattura n. 1/2024",
      "kind": "cashbook",
      "type": "in",
      "amount_in": 1220.00,
      "payment_account_in": {"id": 1},
      "entity_name": "Mario Rossi Srl"
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/cashbook"
```

**Outgoing payment (uscita):**

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "date": "2024-01-20",
      "description": "Pagamento fornitore",
      "kind": "cashbook",
      "type": "out",
      "amount_out": 610.00,
      "payment_account_out": {"id": 2},
      "entity_name": "Fornitore SpA"
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/cashbook"
```

**Kind values:**

| Value | Description |
|---|---|
| `cashbook` | Movimento di prima nota manuale |
| `incoming` | Entrata collegata a un documento attivo |
| `outgoing` | Uscita collegata a un documento passivo |

> `amount_in` and `amount_out` are mutually exclusive for a single entry.
> `payment_account_in`/`payment_account_out` reference payment accounts by ID (from `GET /info/payment_accounts`).

---

## Get Cashbook Entry — `GET /c/{company_id}/cashbook/{document_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/cashbook/$ENTRY_ID"
```

---

## Update Cashbook Entry — `PUT /c/{company_id}/cashbook/{document_id}`

```bash
curl -s -X PUT \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "date": "2024-01-15",
      "description": "Incasso fattura n. 1/2024 - rettificato",
      "kind": "cashbook",
      "type": "in",
      "amount_in": 1250.00,
      "payment_account_in": {"id": 1}
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/cashbook/$ENTRY_ID"
```

---

## Delete Cashbook Entry — `DELETE /c/{company_id}/cashbook/{document_id}`

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/cashbook/$ENTRY_ID"
```

---

## Key Fields Reference

| Field | Description |
|---|---|
| `date` | Data del movimento (YYYY-MM-DD) |
| `description` | Descrizione del movimento |
| `kind` | `cashbook`, `incoming`, o `outgoing` |
| `type` | `in` (entrata) o `out` (uscita) |
| `amount_in` | Importo entrata (usare con `type: in`) |
| `amount_out` | Importo uscita (usare con `type: out`) |
| `payment_account_in` | Conto di destinazione per entrate `{"id": N}` |
| `payment_account_out` | Conto di origine per uscite `{"id": N}` |
| `entity_name` | Nome del cliente/fornitore associato |

---

## Error Reference

| HTTP | Cause | Fix |
|---|---|---|
| `422` | Both `amount_in` and `amount_out` set | Use only one per entry |
| `422` | Invalid `payment_account_in/out.id` | Check via `/info/payment_accounts` |
| `422` | Missing `date` or `kind` | Both are required |
| `404` | Entry not found | Verify `$ENTRY_ID` |
