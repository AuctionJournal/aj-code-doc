[Auction Journal](../index.md) · [Auctioneer Client](index.md)

# Import and export customers (CSV)

Auctioneers with many existing contacts can **bulk-import** customers into their Auction Journal account instead of entering them one by one. They can also **export** current clients or download a **sample CSV** to match the expected column layout.

**User walkthrough:** [How do I import many existing customers?](../user_side_doc/auctioneer-client/import-clients.md)

---

## Business meaning

- Each imported row becomes a **`clientsOfAuctioneer`** record scoped to the signed-in auctioneer.
- Import is intended for **initial migration** or periodic bulk adds from spreadsheets (Excel exported as CSV, another CRM, etc.).
- Imported clients are created with **`isBuyer: true`** and **`isConsigner: true`** so they can participate as both buyer and seller until you refine accounting on individual profiles.
- **Export** downloads all clients for the auctioneer in the same column shape as the sample file—useful as a backup or as a starting template for the next import.
- **Overwrite** (optional) updates existing clients matched by **`clientCode`** when validation passes; without overwrite, rows whose `clientCode` or `clientMail` already exist for that auctioneer are skipped or rejected per validation rules.

Manual single-client create remains separate: [Building / adding a client](./build.md).

---

## Dashboard entry (`auctioneer_dashboard_revamp`)

| Location | Detail |
|----------|--------|
| Route | `/dashboard/clients` → `ClientHome` (`clientHome.jsx`) |
| Actions | **Import Clients**, **Export Clients** (top action bar, beside Add / Onboard Seller / Onboard Floor Bidder) |
| Import UI | `ImportClient` modal wizard — `src/Components/Client/ExportImportClient/index.jsx` |
| API helpers | `src/lib/api/clients/import-export.js` |

### Import wizard steps (frontend)

| Step | Component | Behavior |
|------|-----------|----------|
| 0 | `UploadFiles` | Download sample CSV (`GET` sample API); upload `.csv` via Papa Parse; first row treated as headers |
| 1 | `FeildMapping` | Map CSV column **indices** to AJ fields (`mapping_columns` + hidden defaults for `clientType`, `clientCode`) |
| 2 | `ClientsPreview` | Table preview of parsed rows using mapped indices |
| 3 | `OverwriteData` | Checkbox **Overwrite Existing Data**; submits `multipart/form-data` import |
| 4 | `ClientImportResult` | Shows `rowsProcessed`, `rowsInserted`, `newUsers`, `warnings`, `errors`; optional **Download Report** |

Column mapping object keys sent to the API (values are **0-based column indexes** in each CSV row):

| Key | UI label (step 1) | Notes |
|-----|-------------------|--------|
| `clientType` | (not shown; default index `0`) | Sample CSV column `clientType` |
| `clientCode` | (not shown; default index `1`) | Required for backend validation |
| `companyName` | Company Name | |
| `firstName` | First Name | |
| `lastName` | Last Name | |
| `phone1` | Phone 1 | |
| `phone2` | Phone 2 | |
| `clientMail` | Email | Sample file header is `email` |
| `dlNumber` | DL Number | |
| `dob` | DOB | |
| `auctionFoundFrom` | Auction Found From | |
| `notes` | Notes | Optional in Yup schema |

Frontend `FormData` on import: `file`, `mapping` (JSON string of index map), `isOverwrite` (boolean). `isIncludeFirstRow` exists in the API helper but is not set by the revamp wizard.

---

## Backend (`AJ-Main-Backend`)

Routes: `app/routes/auctioneer-clients.js`  
Handlers: `app/controllers/auctioneer/client/clientCSV.js`  
Sample file on disk: `app/controllers/auctioneer/client/download/sample_CSV.csv`

| Method | Path | Handler | Auth | Purpose |
|--------|------|---------|------|---------|
| `GET` | `/api/auctioneer/clients/download/sampleCSV` | `downloadSampleCSV` | Auctioneer | Download `sample_CSV.csv` template |
| `POST` | `/api/clients/download` | `exportClientsInCSV` | Auctioneer | Export all clients for `req.user.id` as CSV |
| `POST` | `/api/auctioneer/clients/import` | `importClientFromCSV` | Auctioneer | `upload.single("file")` — parse CSV, validate, insert/update |
| `POST` | `/api/auctioneer/clients/import/report` | `downloadClientImportErrors` | Auctioneer | Body `{ errorReportFilename }` — download row error CSV |
| `GET` | `/api/auctioneer/clients/import/report` | `getClientImportErrorReport` | Auctioneer | Legacy error report by auctioneer id |

### Sample CSV columns

```text
clientType, clientCode, companyName, firstName, middleName, lastName,
phone1, phone2, email, dlNumber, dob, auctionFoundFrom, notes
```

Export uses the same logical fields (`clientType` derived from `isBuyer` / `isConsigner` flags).

### `importClientFromCSV` flow

1. Read uploaded file from multer temp path; parse with `csv-parse`.
2. **`records.slice(1)`** — first row of file is always skipped (treated as header); data rows only.
3. Build payloads with `auctioneer`, `isBuyer: true`, `isConsigner: true`, and fields from `mapping` column indices.
4. Load existing clients for this auctioneer matching any `clientCode` or `clientMail` in the file.
5. **`clientsImportValidation`** — per row:
   - Requires `clientCode`, `companyName`, first/last name, `phone1`, valid `clientMail`.
   - Duplicate email vs another existing client (same auctioneer) → error unless overwrite path applies.
   - Match existing row by **`clientCode`** for update when `isOverwrite` is true.
6. If invalid → `400`, `errorReportFilename` written under `download/`, auto-deleted after 30 minutes.
7. If valid → `insertMany` new rows; if `isOverwrite`, `bulkWrite` updates for matched `_id`s.
8. Response `result`: `{ rowsProcessed, rowsInserted, newUsers, warning, errors }`, `isValid`, optional `errorReportFilename`.

### Export

`exportClientsInCSV` — `AuctioneerClients.find({ auctioneer })`, maps to flat objects, `json2csv`, attachment `my_clients_AJ-{auctioneerId}.csv`. Frontend saves download as `my_AJ_Clients.csv`.

---

## Related

- [Ways customers are created](./creation-paths.md) — import as creation path #2  
- [Building / adding a client](./build.md) — manual alternative  
- [User: import many customers](../user_side_doc/auctioneer-client/import-clients.md)
