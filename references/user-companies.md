# User & Companies

Endpoints for reading authenticated user info and listing/inspecting companies.

> **Note:** `/user/info` and `/user/companies` are **not** company-scoped — they do not use `$FIC_COMPANY_ID`.

---

## User

### Get User Info — `GET /user/info`

Returns details about the authenticated user (name, email, etc.).

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/user/info"
```

### List User Companies — `GET /user/companies`

Returns all companies the authenticated user can access. Use this to find your `$FIC_COMPANY_ID`.

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/user/companies"
```

Example response (abbreviated):

```json
{
  "data": {
    "companies": [
      {"id": 12345, "name": "Acme Srl", "type": "company", "access_token": "..."},
      {"id": 67890, "name": "Studio Rossi", "type": "accountant_company"}
    ]
  }
}
```

---

## Companies

### Get Company Info — `GET /c/{company_id}/company/info`

Returns the company's details (name, tax code, VAT number, fiscal data, etc.).

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/company/info"
```

### Get Company Plan Usage — `GET /c/{company_id}/company/plan_usage`

Returns current usage against plan limits (documents created, storage used, etc.).

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/company/plan_usage"
```

Example response (abbreviated):

```json
{
  "data": {
    "limits": {
      "issued_documents": {"limit": 500, "used": 143},
      "received_documents": {"limit": 200, "used": 67},
      "storage": {"limit_bytes": 1073741824, "used_bytes": 204800}
    }
  }
}
```

---

## Error Reference

| HTTP | Cause | Fix |
|---|---|---|
| `401` | Token expired or missing | Check `$FIC_ACCESS_TOKEN` |
| `403` | Token lacks user/company scope | Verify OAuth2 scopes |
| `404` | Company ID not found or not accessible | Run `GET /user/companies` to find valid IDs |
