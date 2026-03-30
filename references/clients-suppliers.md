# Clients & Suppliers

Full CRUD for clients (`/entities/clients`) and suppliers (`/entities/suppliers`). Both follow the same pattern.

---

## Clients

### List Clients — `GET /c/{company_id}/entities/clients`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/clients"
```

With search and pagination:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/clients?q=Rossi&page=1&per_page=50"
```

### Create Client — `POST /c/{company_id}/entities/clients`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "name": "Mario Rossi Srl",
      "vat_number": "IT12345678901",
      "tax_code": "RSSMRA80A01H501Z",
      "address_street": "Via Roma 1",
      "address_city": "Milano",
      "address_province": "MI",
      "address_postal_code": "20121",
      "address_country": "IT",
      "email": "mario@example.com",
      "phone": "+39 02 1234567"
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/clients"
```

### Get Client — `GET /c/{company_id}/entities/clients/{client_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/clients/$CLIENT_ID"
```

### Update Client — `PUT /c/{company_id}/entities/clients/{client_id}`

```bash
curl -s -X PUT \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "email": "nuovo@example.com",
      "phone": "+39 02 9876543"
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/clients/$CLIENT_ID"
```

### Delete Client — `DELETE /c/{company_id}/entities/clients/{client_id}`

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/clients/$CLIENT_ID"
```

### Client Info (form metadata) — `GET /c/{company_id}/entities/clients/info`

Returns reference data for building client forms: available countries, default payment methods, etc.

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/clients/info"
```

---

## Suppliers

Same pattern as clients, under `/entities/suppliers`.

### List Suppliers — `GET /c/{company_id}/entities/suppliers`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/suppliers?q=Fornitore&page=1&per_page=50"
```

### Create Supplier — `POST /c/{company_id}/entities/suppliers`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "name": "Fornitore SpA",
      "vat_number": "IT98765432109",
      "tax_code": "FRNXXX80A01H501Z",
      "address_street": "Via Torino 10",
      "address_city": "Roma",
      "address_province": "RM",
      "address_postal_code": "00100",
      "address_country": "IT",
      "email": "contatti@fornitore.it"
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/suppliers"
```

### Get Supplier — `GET /c/{company_id}/entities/suppliers/{supplier_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/suppliers/$SUPPLIER_ID"
```

### Update Supplier — `PUT /c/{company_id}/entities/suppliers/{supplier_id}`

```bash
curl -s -X PUT \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "email": "nuovo@fornitore.it"
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/suppliers/$SUPPLIER_ID"
```

### Delete Supplier — `DELETE /c/{company_id}/entities/suppliers/{supplier_id}`

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/entities/suppliers/$SUPPLIER_ID"
```

---

## Key Fields Reference

| Field | Description |
|---|---|
| `name` | Business name or full name (required) |
| `vat_number` | Partita IVA (e.g. `IT12345678901`) |
| `tax_code` | Codice fiscale |
| `address_country` | ISO 3166-1 alpha-2 country code (e.g. `IT`, `DE`, `US`) |
| `e_invoice` | `true` to enable electronic invoicing for this client |
| `ei_code` | Codice destinatario SDI (7 chars) — required for e-invoice |
| `pec` | PEC address — alternative to `ei_code` for e-invoice delivery |
| `default_payment_terms` | Default payment days |
| `default_payment_terms_type` | `standard` or `end_of_month` |

---

## Error Reference

| HTTP | Cause | Fix |
|---|---|---|
| `404` | Client/supplier ID not found | Verify the ID; may have been deleted |
| `422` | Validation error | Check `vat_number` format, required fields |
