# Webhooks (Subscriptions)

Gestione delle sottoscrizioni agli eventi webhook. FattureInCloud invia una richiesta HTTP POST all'URL configurato ogni volta che si verifica un evento. All paths are under `/c/$FIC_COMPANY_ID/subscriptions`.

---

## List Subscriptions — `GET /c/{company_id}/subscriptions`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/subscriptions"
```

---

## Create Subscription — `POST /c/{company_id}/subscriptions`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "sink": {
        "url": "https://your-server.example.com/webhook",
        "type": "webhook"
      },
      "types": [
        "it.fattureincloud.api.issued_documents.create",
        "it.fattureincloud.api.issued_documents.update",
        "it.fattureincloud.api.issued_documents.delete"
      ]
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/subscriptions"
```

---

## Get Subscription — `GET /c/{company_id}/subscriptions/{subscription_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/subscriptions/$SUBSCRIPTION_ID"
```

---

## Update Subscription — `PUT /c/{company_id}/subscriptions/{subscription_id}`

```bash
curl -s -X PUT \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "sink": {
        "url": "https://your-server.example.com/webhook-v2",
        "type": "webhook"
      },
      "types": [
        "it.fattureincloud.api.issued_documents.create",
        "it.fattureincloud.api.clients.create",
        "it.fattureincloud.api.clients.update"
      ]
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/subscriptions/$SUBSCRIPTION_ID"
```

---

## Delete Subscription — `DELETE /c/{company_id}/subscriptions/{subscription_id}`

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/subscriptions/$SUBSCRIPTION_ID"
```

---

## Verify Subscription — `POST /c/{company_id}/subscriptions/{subscription_id}/verify`

Sends a test ping to the configured URL to verify the endpoint is reachable:

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"data": {}}' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/subscriptions/$SUBSCRIPTION_ID/verify"
```

---

## Available Event Types

| Event | Triggered when |
|---|---|
| `it.fattureincloud.api.issued_documents.create` | Documento emesso creato |
| `it.fattureincloud.api.issued_documents.update` | Documento emesso modificato |
| `it.fattureincloud.api.issued_documents.delete` | Documento emesso eliminato |
| `it.fattureincloud.api.received_documents.create` | Documento ricevuto creato |
| `it.fattureincloud.api.received_documents.update` | Documento ricevuto modificato |
| `it.fattureincloud.api.received_documents.delete` | Documento ricevuto eliminato |
| `it.fattureincloud.api.clients.create` | Cliente creato |
| `it.fattureincloud.api.clients.update` | Cliente modificato |
| `it.fattureincloud.api.clients.delete` | Cliente eliminato |
| `it.fattureincloud.api.suppliers.create` | Fornitore creato |
| `it.fattureincloud.api.suppliers.update` | Fornitore modificato |
| `it.fattureincloud.api.suppliers.delete` | Fornitore eliminato |
| `it.fattureincloud.api.products.create` | Prodotto creato |
| `it.fattureincloud.api.products.update` | Prodotto modificato |
| `it.fattureincloud.api.products.delete` | Prodotto eliminato |

---

## Webhook Payload Structure

FattureInCloud sends a POST to your URL with:

```json
{
  "event_type": "it.fattureincloud.api.issued_documents.create",
  "company_id": 12345,
  "data": {
    "id": 789,
    ...
  }
}
```

Your endpoint must respond with HTTP `2xx` within a reasonable timeout.

---

## Error Reference

| HTTP | Cause | Fix |
|---|---|---|
| `422` | Invalid `sink.url` (not HTTPS) | Webhook URLs must use HTTPS |
| `422` | Invalid event type in `types` | Use exact event type strings from the table above |
| `404` | Subscription ID not found | Verify `$SUBSCRIPTION_ID` |
