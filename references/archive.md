# Archive

Archiviazione di documenti generici (contratti, documenti amministrativi, ecc.) non classificabili come fatture o spese. All paths are under `/c/$FIC_COMPANY_ID/archive`.

---

## List Archive Documents — `GET /c/{company_id}/archive`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/archive"
```

With pagination and search:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/archive?page=1&per_page=50&q=contratto"
```

---

## Create Archive Document — `POST /c/{company_id}/archive`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "date": "2024-01-15",
      "description": "Contratto di locazione ufficio",
      "category": {"id": 1},
      "attachment_token": "abc123token"
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/archive"
```

> **`category`** references an archive category by ID. Fetch available categories with `GET /info/archive_categories` (see `references/info.md`).
> **`attachment_token`** is obtained by uploading the file first (see below).

---

## Get Archive Document — `GET /c/{company_id}/archive/{document_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/archive/$DOCUMENT_ID"
```

---

## Update Archive Document — `PUT /c/{company_id}/archive/{document_id}`

```bash
curl -s -X PUT \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "date": "2024-01-15",
      "description": "Contratto di locazione ufficio - aggiornato",
      "category": {"id": 1}
    }
  }' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/archive/$DOCUMENT_ID"
```

---

## Delete Archive Document — `DELETE /c/{company_id}/archive/{document_id}`

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/archive/$DOCUMENT_ID"
```

---

## Upload Attachment — `POST /c/{company_id}/archive/attachment`

Upload the file before creating the archive document:

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -F "attachment=@/path/to/contratto.pdf" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/archive/attachment"
```

Returns the token to use in the document body:

```json
{"data": {"attachment_token": "abc123token"}}
```

---

## Typical Workflow

```bash
# 1. Get available categories
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/info/archive_categories"

# 2. Upload the file
TOKEN=$(curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -F "attachment=@/path/to/documento.pdf" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/archive/attachment" \
  | python3 -c "import sys,json; print(json.load(sys.stdin)['data']['attachment_token'])")

# 3. Create the archive document
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d "{\"data\": {\"date\": \"2024-01-15\", \"description\": \"Contratto\", \"category\": {\"id\": 1}, \"attachment_token\": \"$TOKEN\"}}" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/archive"
```

---

## Error Reference

| HTTP | Cause | Fix |
|---|---|---|
| `422` | Invalid `category.id` | Check IDs via `/info/archive_categories` |
| `422` | Missing `description` | Required field |
| `404` | Document not found | Verify `$DOCUMENT_ID` |
