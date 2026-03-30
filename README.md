# FattureInCloud Skill per Claude Code

Skill per interfacciarsi con l'**API v2 di FattureInCloud** direttamente da Claude Code. Copre tutti i principali domini: fatture attive e passive, clienti, fornitori, prodotti, corrispettivi, F24, prima nota, archivio, fatturazione elettronica SDI e webhook.

---

## Funzionalità

| Dominio | Operazioni |
|---|---|
| Utente e aziende | Info utente, lista aziende, info azienda, piano usage |
| Clienti e fornitori | Crea, leggi, aggiorna, elimina, cerca |
| Prodotti | Crea, leggi, aggiorna, elimina, cerca |
| Documenti emessi | Fatture, preventivi, ordini, DDT, note di credito — CRUD completo + allegati, email, trasformazione |
| Fatturazione elettronica | Verifica XML, invio SDI, download XML, motivi di rifiuto |
| Documenti ricevuti | Fatture passive CRUD + gestione documenti in attesa |
| Corrispettivi | CRUD + totali mensili |
| F24 | CRUD + allegati |
| Archivio | CRUD documenti generici + allegati |
| Prima nota | CRUD movimenti entrate/uscite con filtro per data |
| Impostazioni | Metodi pagamento, conti, aliquote IVA, profilo fiscale, template |
| Webhook | Sottoscrizioni eventi CRUD + verifica endpoint |
| Cestino | Ripristino e cancellazione permanente di documenti eliminati |

---

## Prerequisiti

- **Claude Code** installato (`npm install -g @anthropic-ai/claude-code`)
- Un account FattureInCloud con accesso API
- Un **OAuth2 Access Token** ottenuto tramite l'app developer di FattureInCloud

### Ottenere le credenziali

1. Accedi a [developers.fattureincloud.it](https://developers.fattureincloud.it/)
2. Crea una nuova applicazione OAuth2
3. Completa il flusso di autorizzazione (Authorization Code Flow o Device Code Flow)
4. Copia l'access token e il company ID della tua azienda

---

## Installazione

### 1. Copia la skill nella directory di Claude Code

```bash
cp -r /path/to/ficskill ~/.claude/skills/fattureincloud
```

Oppure, se stai clonando da questo repository:

```bash
git clone <repo-url> ~/.claude/skills/fattureincloud
```

### 2. Configura le variabili d'ambiente

Aggiungi queste righe al tuo `~/.zshrc` o `~/.bashrc`:

```bash
export FIC_ACCESS_TOKEN="il_tuo_access_token_oauth2"
export FIC_COMPANY_ID="il_tuo_company_id"
```

Poi ricarica la shell:

```bash
source ~/.zshrc
```

### 3. Verifica l'installazione

Controlla che la skill sia disponibile:

```bash
claude skills list
```

Dovresti vedere `fattureincloud` nell'elenco.

### 4. Test rapido

```bash
curl -s \
  -H "Authorization: Bearer $FIC_ACCESS_TOKEN" \
  "https://api-v2.fattureincloud.it/user/companies" | python3 -m json.tool
```

Se ottieni la lista delle aziende, le credenziali funzionano correttamente.

---

## Utilizzo

Una volta installata, la skill si attiva automaticamente in Claude Code quando fai domande o richieste relative a FattureInCloud. Esempi:

```
"Crea una fattura per il cliente Mario Rossi per 2 ore di consulenza a 100€ + IVA 22%"
"Lista tutte le fatture di gennaio 2024"
"Invia la fattura n. 5/2024 via email al cliente"
"Aggiungi un fornitore con P.IVA IT12345678901"
"Mostra i corrispettivi mensili del 2024"
"Invia la fattura elettronica n. 3/2024 all'SDI"
```

---

## Struttura della Skill

```
fattureincloud/
├── SKILL.md                    ← Entry point: auth, convenzioni, quick reference
├── README.md                   ← Questo file
└── references/
    ├── user-companies.md       ← Utente e aziende
    ├── clients-suppliers.md    ← Clienti e fornitori
    ├── products.md             ← Prodotti e servizi
    ├── issued-documents.md     ← Documenti emessi (fatture, preventivi…)
    ├── e-invoices.md           ← Fatturazione elettronica SDI
    ├── received-documents.md   ← Documenti ricevuti/passivi
    ├── receipts.md             ← Corrispettivi
    ├── taxes-f24.md            ← F24
    ├── archive.md              ← Archivio documenti
    ├── cashbook.md             ← Prima nota
    ├── info.md                 ← Dati di riferimento (IVA, pagamenti…)
    ├── settings.md             ← Impostazioni azienda
    ├── webhooks.md             ← Webhook e sottoscrizioni eventi
    └── recycle-bin.md          ← Cestino documenti
```

---

## Note importanti

- **Data envelope:** Tutte le richieste write (POST/PUT) richiedono il wrapper `{"data": {...}}`. Tutte le risposte lo restituiscono.
- **`?type=` obbligatorio:** La `GET /issued_documents` richiede sempre il parametro `?type=invoice` (o altro tipo). Senza di esso restituisce 422.
- **Soft delete vs hard delete:** `DELETE /issued_documents/{id}` sposta nel cestino (reversibile). `DELETE /bin/issued_documents/{id}` è **permanente e irreversibile**.
- **Fattura elettronica:** `POST .../e_invoice/send` è irreversibile una volta accettato dall'SDI. Verifica sempre il XML prima con `xml_verify`.

---

## API Reference

- Documentazione ufficiale: [developers.fattureincloud.it](https://developers.fattureincloud.it/)
- OpenAPI spec: [github.com/fattureincloud/openapi-fattureincloud](https://github.com/fattureincloud/openapi-fattureincloud)
- Base URL: `https://api-v2.fattureincloud.it`
- Versione API: v2.1.8
