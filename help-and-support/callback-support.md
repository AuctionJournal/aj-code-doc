[Auction Journal](../index.md) · [Help And Support](./index.md)

# Callback support (Request a Callback)

Users request a **scheduled phone callback** from Auction Journal support. The system creates a ticket with prefix **`RAC_`**, sends confirmation and admin notification emails, and lists the ticket under **Ticket History → CALLBACK REQUESTS**.

**User guide:** [Request a Callback](../user_side_doc/help-and-support/callback-support.md)

---

## Frontend

### Auctioneer

| Item | Location |
|------|----------|
| Hub tile | `auctioneer_dashboard_revamp/src/Components/UserSupport/index.jsx` |
| Modal | `Components/UserSupport/RequestCallback/index.jsx` |
| API client | `createCallbackRequest` → `lib/api/support/index.js` |

**Routes:** Opened from `/dashboard/user-support` (modal only; no dedicated route).

### Bidder

| Item | Location |
|------|----------|
| Hub tile | `auctionjournal-public/src/app/Components/Bidder/Bid_Support/index.jsx` |
| Modal | `CallBack_PopUp/index.jsx` |
| API client | `auctionjournal-public/src/lib/api/support/callback.js` |

**Routes:** Opened from `/bidder/support` (modal).

---

## Two-step form (auctioneer)

| Step | Title | Fields | Validation |
|------|--------|--------|------------|
| 0 | Call Request Information | `reqName`, `desc`, `TimeZone` (disabled), `date`, `time` | `callbackSchema1` (yup) |
| 1 | Client Information | `firstName`, `lastName`, `email`, `phoneNumber` | `callbackSchema2`; phone `^\+1\d{10}$` |

On **Next** from step 0, `date` and `time` are combined into **`callbackDateTime`** (ISO) before submit. Step 1 fields are prefilled from `useUserContext` (`FirstName`, `LastName`, `Phone`, `Email`).

Submit calls `createCallbackRequest` with `date`/`time` stripped; success snackbar: *Message successfully added*.

Bidder modal follows the same business fields with simpler date/time inputs and builds `callbackDateTime` on submit.

---

## API

| Method | Path | Handler |
|--------|------|---------|
| POST | `/api/customer/support/callbackRequest` | `requestACallback` |
| POST | `/api/customer/support/view/callbackRequests` | `getRequestACallbackDetails` |
| POST | `/api/customer/support/view/callbackrequest` | `getCallbackRequestDetails` (body: `{ id }`) |

**Auth:** JWT (`requireAuth`) on all routes (`app/routes/helpAndSupport.js`).

**Create body (main fields):** `reqName`, `desc`, `firstName`, `lastName`, `phoneNumber`, `email`, `callbackDateTime`.

**Server behavior** (`controllers/helpAndSupport/callback-support.js`):

- Sets `TicketID` = `RAC_` + random suffix, `Status: "Raised"`, `userID` / `userType` from JWT.
- Customer email: template type **7**, from `emailConfig.username_support`.
- Admin notification via `sendNotificationEmail`.

There is **no express-validator** on callback create (unlike email support).

---

## Ticket detail and replies

- **Auctioneer:** `/dashboard/user-support/ticket-history/callback` — `CallbackHistory/index.jsx`; ticket passed via **`location.state.ticket`** (no URL id).
- **Bidder:** `/bidder/support/ticket-history/callback/[id]` — fetches via `view/callbackrequest`.

Detail view shows user request block and **admin `comments`** (HTML) in reverse order. **Users cannot reply** on callback tickets in the UI.

Callback status is **not** set to `Open` by backend on user action (only email replies do that).

---

## Limitations (product / support ops)

- Callback is **not** instant chat; it is a **scheduled call request**.
- Closure and admin notes are handled outside this customer UI (admin panel).
- See [Ticket history](./ticket-history.md) for status display (`Raised`, `Open`, `Closed`).

---

## Model

`app/models/requestACallBack.js` — field reference in [Fields](./fields.md).
