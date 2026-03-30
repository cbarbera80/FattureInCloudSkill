# Electronic Invoicing (Fatturazione Elettronica / SDI)

Operations for Italian electronic invoicing via the **Sistema di Interscambio (SDI)**. These endpoints operate on existing issued documents (see `references/issued-documents.md`).

All paths are under `/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID/e_invoice/`.

---

## E-Invoice Status Lifecycle

```
not_sent → sent → accepted
                → rejected  (read error reason, fix XML, re-send)
                → manual_accepted
```

Check the `e_invoice_status` field on the document before sending. Only documents with status `not_sent` or `rejected` can be sent.

---

## Verify XML — `GET /c/{company_id}/issued_documents/{document_id}/e_invoice/xml_verify`

Validate the document's XML against SDI rules **before** sending. Always verify first.

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID/e_invoice/xml_verify"
```

Response includes a `success` flag and any `errors` array:

```json
{
  "data": {
    "success": false,
    "errors": [
      {"code": "00423", "description": "Codice fiscale destinatario non valido"}
    ]
  }
}
```

---

## Send E-Invoice — `POST /c/{company_id}/issued_documents/{document_id}/e_invoice/send`

> **WARNING: This action is irreversible once accepted by SDI.** Verify the XML first. Ensure the client has a valid `ei_code` (codice destinatario) or `pec` address set.

```bash
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"data": {}}' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID/e_invoice/send"
```

After sending, poll the document's `e_invoice_status` to track acceptance:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID" \
  | python3 -m json.tool | grep e_invoice_status
```

---

## Download XML — `GET /c/{company_id}/issued_documents/{document_id}/e_invoice/xml`

Download the generated XML file for the e-invoice:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID/e_invoice/xml" \
  -o "fattura_$DOCUMENT_ID.xml"
```

---

## Get Rejection Reason — `GET /c/{company_id}/issued_documents/{document_id}/e_invoice/error_reason`

When `e_invoice_status` is `rejected`, fetch the SDI rejection details:

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID/e_invoice/error_reason"
```

Example response:

```json
{
  "data": {
    "error_code": "00002",
    "error_description": "File non integro",
    "error_details": "..."
  }
}
```

After fixing the underlying issue (e.g. updating the client's codice destinatario), the document status resets to `not_sent` and you can re-send.

---

## Recommended Workflow

```bash
# 1. Verify XML first
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID/e_invoice/xml_verify"

# 2. If valid, send to SDI
curl -s -X POST \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"data": {}}' \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID/e_invoice/send"

# 3. Poll status
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/c/$FIC_COMPANY_ID/issued_documents/$DOCUMENT_ID" \
  | python3 -c "import sys,json; d=json.load(sys.stdin); print(d['data']['e_invoice_status'])"
```

---

## Client Requirements for E-Invoicing

The client on the invoice must have one of the following set:
- `ei_code`: 7-character codice destinatario SDI
- `pec`: PEC email address for e-invoice delivery
- For private individuals: `ei_code` = `0000000` (sends to SDI, client retrieves from their fiscal drawer)

---

## Error Reference

| HTTP | Cause | Fix |
|---|---|---|
| `422` | Document already sent or not in sendable state | Check `e_invoice_status`; only `not_sent`/`rejected` are sendable |
| `422` | Client missing `ei_code` or `pec` | Update the client record with a valid destination code |
| `422` | XML validation failed | Run xml_verify first; fix reported errors |
