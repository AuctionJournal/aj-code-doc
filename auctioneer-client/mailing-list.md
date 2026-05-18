[Auction Journal](../index.md) · [Auctioneer Client](index.md)

# Auctioneer client mailing lists

Auctioneers group **`clientsOfAuctioneer`** records (and optional raw emails) into **`clientMailingList`** documents for organization, export, and website-subscription capture.

**User guide:** [How can an auctioneer use the mailing list in Customers? What is the benefit?](../user_side_doc/auctioneer-client/mailing-list.md)

---

## Business purpose

- Segment auctioneer CRM clients into named lists (marketing audiences, operational groups).
- Support **CSV export** of list membership for external email tools.
- Sync membership when clients are created/updated (`mailingLists` on add client, `syncClientMailingLists` on edit).
- Auto-maintain **Subscribed From Website** for public newsletter/subscribe flows.

---

## Data model

**Model:** `app/models/clientMailingList.js`

| Field | Meaning |
|-------|---------|
| `name` | List display name |
| `description` | List notes |
| `auctioneer` | Owner `Auctioneer` ref (required) |
| `clients` | Array of `clientsOfAuctioneer` refs |
| `emails` | Raw email strings (non-client subscribers) |
| `isSubScribedFromWebsiteML` | System list flag |

**Member count (UI):** `clients.length + emails.length`.

---

## Dashboard (`auctioneer_dashboard_revamp`)

| Route | Component | Role |
|-------|-----------|------|
| `/dashboard/clients` | `clients.page.jsx` + `ClientHome` | Toggle **CLIENT LIST** / **MAILING LIST** |
| `/dashboard/clients/mailinglists` | `mailingList.page.jsx` + `ClientMailingListHome` | List all MLs, create, search, export, delete |
| `/dashboard/clients/mailinglists/view` | `view-mailingList.page.jsx` + `ViewMailingList` | Edit metadata, `ClientList`, add/remove members |

**Client create/edit:** `BuildClient/ClientInfo` → `ClientMailingLists` dropdown (`fetchMailingLists` with `isSubScribedFromWebsiteML: false`) → passed on `POST /api/client/add` as `mailingLists` → `addClientToLists`.

**List membership UI:** `ClientHandler` dialog — checkbox clients, add all / remove all, saves via `PATCH` edit with full `clients` id array.

---

## API map (`app/routes/auctioneer-clients.js`)

All authenticated routes use `requireAuth` + `allowRoles("Auctioneer")` unless noted.

| Method | Path | Handler | Purpose |
|--------|------|---------|---------|
| `POST` | `/api/auctioneer/client/mailingList/add` | `createNewList` | Create list (`name`, `description`) |
| `POST` | `/api/client/mailingLists` | `fetchLists` | List for auctioneer; body: `searchTerm`, `isSubScribedFromWebsiteML` |
| `GET` | `/api/client/mailingList/:refId` | `fetchList` | One list + populated `clients` |
| `PATCH` | `/api/auctioneer/client/mailingList/edit` | `editList` | Update `mailingListId`, `name`, `description`, `clients` array |
| `DELETE` | `/api/auctioneer/client/mailingList/:refId` | `deleteList` | Delete list; blocks **Subscribed From Website** |
| `POST` | `/api/client/mailingList/clients/add` | `addClientInML` | `refId`, `client` or `isAddAll` + `auctioneer` |
| `DELETE` | `/api/client/mailingList/clients` | `remClientInML` | `refId`, `client` or `isRemAll` |
| `GET` | `/api/client/mailingList/clients/export/:refId` | `exportMLClientsInCSV` | CSV download |
| `POST` | `/api/auctioneer/mailingList/subscribedFromWebsiteML/join` | `subscribedFromWebsiteML` | **Public** — website email subscribe |

**Controller:** `app/controllers/auctioneer/client/mailingList.js` (+ `clientCSV.exportMLClientsInCSV`).

---

## Key behaviors

### Create / edit list

- `createNewList` — `clientMailingList.create({ name, description, auctioneer })`.
- `editList` — `findByIdAndUpdate` with `name`, `description`, `clients` (full membership replace when UI saves from `ClientHandler`).

### Add / remove clients

- `addClientInML` — `$addToSet` one client, or replace `clients` with all auctioneer client ids when `isAddAll`.
- `remClientInML` — `$pull` one client, or `clients: []` when `isRemAll`.
- `addClientToLists` — on manual client create, foreach selected list id `$addToSet` client.
- `syncClientMailingLists` — on client edit: pull from lists not selected, add to selected.
- `deleteClientInML` — when client deleted globally, pull from all lists.

### Website system list

- `subscribedFromWebsiteML` — finds or creates list with `name: "Subscribed From Website"`, `isSubScribedFromWebsiteML: true`.
- Adds matching `clientsOfAuctioneer` by email or appends to `emails`; duplicate email rejected; sends `bidderJoiningAuctioneerMailingList` email.

### Protected delete

- `deleteList` returns `400` if `name === "Subscribed From Website"`.

---

## Related

- [Client fields](./fields.md)  
- [Building / add client](./build.md) — `mailingLists` on create  
- [Creation paths](./creation-paths.md)
