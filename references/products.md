# Products

Full CRUD for the product/service catalog. Products are referenced in document line items.

---

## List Products — `GET /c/{company_id}/products`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/products"
```

With search, category filter, and pagination:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/products?q=consulenza&page=1&per_page=50"
```

---

## Create Product — `POST /c/{company_id}/products`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "code": "CONS-001",
      "name": "Consulenza oraria",
      "description": "Servizio di consulenza informatica",
      "net_price": 100.00,
      "gross_price": 122.00,
      "use_gross_price": false,
      "vat": {"id": 3},
      "category": "Servizi",
      "notes": "IVA 22%"
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/products"
```

> **Pricing:** Set `use_gross_price: false` to use `net_price` as the base (default). Set `use_gross_price: true` to use `gross_price` as the base; `net_price` is then computed automatically.
>
> **VAT:** The `vat` field is an object with an `id` referencing a VAT type. Fetch available IDs with `GET /info/vat_types` (see `references/info.md`).

---

## Get Product — `GET /c/{company_id}/products/{product_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/products/$PRODUCT_ID"
```

---

## Update Product — `PUT /c/{company_id}/products/{product_id}`

```bash
curl -s -X PUT \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "net_price": 110.00,
      "gross_price": 134.20
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/products/$PRODUCT_ID"
```

---

## Delete Product — `DELETE /c/{company_id}/products/{product_id}`

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/products/$PRODUCT_ID"
```

---

## Key Fields Reference

| Field | Description |
|---|---|
| `code` | Product/SKU code (optional but recommended) |
| `name` | Product name (required) |
| `description` | Extended description shown on documents |
| `net_price` | Price excluding VAT |
| `gross_price` | Price including VAT |
| `use_gross_price` | `false` = net base; `true` = gross base |
| `vat` | Object `{"id": N}` — reference from `/info/vat_types` |
| `category` | Free-text category label |
| `measure` | Unit of measure (e.g. `ore`, `pz`, `kg`) |
| `stock_initial` | Initial stock quantity |

---

## Error Reference

| HTTP | Cause | Fix |
|---|---|---|
| `404` | Product ID not found | Verify `$PRODUCT_ID` |
| `422` | Missing required field or invalid VAT id | Check `name` is set; verify `vat.id` via `/info/vat_types` |
