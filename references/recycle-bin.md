# Recycle Bin (Cestino)

Gestione dei documenti eliminati (soft delete). The recycle bin stores deleted issued and received documents before permanent deletion.

> **Important distinction:**
> - `DELETE /issued_documents/{id}` → **soft delete** (reversible, goes to bin)
> - `DELETE /bin/issued_documents/{id}` → **permanent delete** (irreversible, cannot be recovered)

All paths are under `/c/$FIC_COMPANY_ID/bin`.

---

## Issued Documents Bin

### List Deleted Issued Documents — `GET /c/{company_id}/bin/issued_documents`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/bin/issued_documents"
```

With pagination:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/bin/issued_documents?page=1&per_page=50"
```

### Get Deleted Issued Document — `GET /c/{company_id}/bin/issued_documents/{document_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/bin/issued_documents/$DOCUMENT_ID"
```

### Restore Issued Document — `POST /c/{company_id}/bin/issued_documents/{document_id}/recover`

Move the document back from the bin to the active documents list:

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"data": {}}' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/bin/issued_documents/$DOCUMENT_ID/recover"
```

### Permanently Delete Issued Document — `DELETE /c/{company_id}/bin/issued_documents/{document_id}`

> **This action is permanent and cannot be undone.** Always `GET` the document first to confirm it is the correct one before deleting permanently.

```bash
# 1. Verify first
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/bin/issued_documents/$DOCUMENT_ID"

# 2. Permanently delete only if confirmed
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/bin/issued_documents/$DOCUMENT_ID"
```

---

## Received Documents Bin

Same four operations, mirrored under `/bin/received_documents/`.

### List Deleted Received Documents — `GET /c/{company_id}/bin/received_documents`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/bin/received_documents"
```

### Get Deleted Received Document — `GET /c/{company_id}/bin/received_documents/{document_id}`

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/bin/received_documents/$DOCUMENT_ID"
```

### Restore Received Document — `POST /c/{company_id}/bin/received_documents/{document_id}/recover`

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"data": {}}' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/bin/received_documents/$DOCUMENT_ID/recover"
```

### Permanently Delete Received Document — `DELETE /c/{company_id}/bin/received_documents/{document_id}`

> **Irreversible.** Verify the document with a GET before deleting.

```bash
curl -s -X DELETE \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/bin/received_documents/$DOCUMENT_ID"
```

---

## Delete Lifecycle Summary

```
Active document
    │
    └─ DELETE /issued_documents/{id}
            │
            ▼
    Recycle Bin (/bin/issued_documents/{id})
            │
            ├─ POST .../recover  →  Active document (restored)
            │
            └─ DELETE .../bin/{id}  →  GONE FOREVER
```

---

## Error Reference

| HTTP | Cause | Fix |
|---|---|---|
| `404` | Document not in bin | It may already be restored or permanently deleted |
| `422` | Cannot restore document | Possible numbering conflict with another document |
