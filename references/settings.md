# Settings

Configurazione dell'azienda: metodi di pagamento, conti, aliquote IVA, profilo fiscale e template. All paths are under `/c/$FIC_COMPANY_ID/settings`.

---

## Payment Methods

### List Payment Methods — `GET /c/{company_id}/settings/payment_methods`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/payment_methods"
```

### Create Payment Method — `POST /c/{company_id}/settings/payment_methods`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "name": "Bonifico bancario",
      "type": "bank",
      "is_default": false,
      "payment_buffer_days": 30,
      "details": [
        {"title": "IBAN", "description": "IT60X0542811101000000123456"}
      ]
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/payment_methods"
```

**Type values:** `standard`, `bank`, `credit_card`

### Get Payment Method — `GET /c/{company_id}/settings/payment_methods/{payment_method_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/payment_methods/$PAYMENT_METHOD_ID"
```

### Update Payment Method — `PUT /c/{company_id}/settings/payment_methods/{payment_method_id}`

```bash
curl -s -X PUT \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "name": "Bonifico bancario 60gg",
      "type": "bank",
      "payment_buffer_days": 60
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/payment_methods/$PAYMENT_METHOD_ID"
```

### Delete Payment Method — `DELETE /c/{company_id}/settings/payment_methods/{payment_method_id}`

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/payment_methods/$PAYMENT_METHOD_ID"
```

---

## Payment Accounts

### List Payment Accounts — `GET /c/{company_id}/settings/payment_accounts`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/payment_accounts"
```

### Create Payment Account — `POST /c/{company_id}/settings/payment_accounts`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "name": "Conto corrente principale",
      "type": "bank",
      "iban": "IT60X0542811101000000123456",
      "sia": "",
      "cuc": "",
      "virtual": false
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/payment_accounts"
```

**Type values:** `standard` (cassa contanti), `bank` (conto bancario)

### Get Payment Account — `GET /c/{company_id}/settings/payment_accounts/{payment_account_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/payment_accounts/$PAYMENT_ACCOUNT_ID"
```

### Update Payment Account — `PUT /c/{company_id}/settings/payment_accounts/{payment_account_id}`

```bash
curl -s -X PUT \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "name": "Conto corrente principale - aggiornato",
      "type": "bank"
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/payment_accounts/$PAYMENT_ACCOUNT_ID"
```

### Delete Payment Account — `DELETE /c/{company_id}/settings/payment_accounts/{payment_account_id}`

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/payment_accounts/$PAYMENT_ACCOUNT_ID"
```

---

## VAT Types

### List VAT Types — `GET /c/{company_id}/settings/vat_types`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/vat_types"
```

### Create VAT Type — `POST /c/{company_id}/settings/vat_types`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "value": 5,
      "description": "IVA 5% beni di prima necessità",
      "notes": "Aliquota ridotta",
      "e_invoice": true,
      "ei_type": "I",
      "ei_description": "Iva al 5%",
      "is_disabled": false
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/vat_types"
```

### Get VAT Type — `GET /c/{company_id}/settings/vat_types/{vat_type_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/vat_types/$VAT_TYPE_ID"
```

### Update VAT Type — `PUT /c/{company_id}/settings/vat_types/{vat_type_id}`

```bash
curl -s -X PUT \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "value": 5,
      "description": "IVA 5% - aggiornata",
      "is_disabled": false
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/vat_types/$VAT_TYPE_ID"
```

### Delete VAT Type — `DELETE /c/{company_id}/settings/vat_types/{vat_type_id}`

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/vat_types/$VAT_TYPE_ID"
```

---

## Tax Profile (Read-Only)

Returns fiscal regime, SDI configuration, and electronic invoicing settings. Cannot be modified via API.

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/tax_profile"
```

---

## Templates (Read-Only)

### List Templates — `GET /c/{company_id}/settings/templates`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/templates"
```

### Get Template — `GET /c/{company_id}/settings/templates/{template_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/settings/templates/$TEMPLATE_ID"
```

> Templates define the visual layout for printed/PDF documents. They can only be managed via the FattureInCloud web interface.

---

## Error Reference

| HTTP | Cause | Fix |
|---|---|---|
| `422` | Missing required `name` | Required for payment methods and accounts |
| `422` | Invalid `type` value | Use only allowed values listed above |
| `404` | Resource ID not found | Verify the ID via the corresponding list endpoint |
