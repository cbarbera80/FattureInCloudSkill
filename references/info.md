# Reference Data (Info)

Read-only endpoints that return reference/lookup data for building documents. All are `GET` with no write operations. Fetch these once and cache them locally — they change rarely.

All paths are under `/c/$FIC_COMPANY_ID/info/`.

---

## Available Endpoints

| Endpoint | Returns |
|---|---|
| `GET /info/vat_types` | VAT rates available for this company (id, value, description) |
| `GET /info/payment_methods` | Payment method objects with id and name |
| `GET /info/payment_accounts` | Bank accounts and cash accounts |
| `GET /info/revenue_centers` | Revenue center objects (optional on issued documents) |
| `GET /info/cost_centers` | Cost center objects (optional on received documents) |
| `GET /info/product_categories` | Product category list |
| `GET /info/received_document_categories` | Categories for received documents |
| `GET /info/archive_categories` | Categories for archive documents |

---

## VAT Types — `GET /info/vat_types`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/info/vat_types"
```

Example response (abbreviated):

```json
{
  "data": [
    {"id": 3, "value": 22, "description": "IVA 22%", "is_disabled": false},
    {"id": 4, "value": 10, "description": "IVA 10%", "is_disabled": false},
    {"id": 5, "value": 4,  "description": "IVA 4%",  "is_disabled": false},
    {"id": 6, "value": 0,  "description": "Esente IVA art. 10", "is_disabled": false}
  ]
}
```

> Use the `id` field when referencing a VAT type in documents (`"vat": {"id": 3}`).

---

## Payment Methods — `GET /info/payment_methods`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/info/payment_methods"
```

---

## Payment Accounts — `GET /info/payment_accounts`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/info/payment_accounts"
```

---

## Revenue Centers — `GET /info/revenue_centers`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/info/revenue_centers"
```

---

## Cost Centers — `GET /info/cost_centers`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/info/cost_centers"
```

---

## Product Categories — `GET /info/product_categories`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/info/product_categories"
```

---

## Received Document Categories — `GET /info/received_document_categories`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/info/received_document_categories"
```

---

## Archive Categories — `GET /info/archive_categories`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/info/archive_categories"
```

---

## Tip: Fetch All Reference Data at Once

```bash
BASE="https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/info"
AUTH="-H \"Authorization: Bearer $FIC_ACCESS_TOKEN\""

for resource in vat_types payment_methods payment_accounts revenue_centers cost_centers product_categories received_document_categories archive_categories; do
  echo "=== $resource ==="
  curl -s -H "Authorization: Bearer $FIC_ACCESS_TOKEN" "$BASE/$resource" | python3 -m json.tool
done
```
