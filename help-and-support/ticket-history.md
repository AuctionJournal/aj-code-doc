[Auction Journal](../index.md) · [Help And Support](./index.md)

# Ticket history

Authenticated users view **their own** callback and email support tickets from **Ticket History** on the GET IN TOUCH hub. Email tickets support **continuing the conversation** via **REPLY TO ADMIN**; callback tickets are **view-only** for the user.

**User guide:** [Ticket history](../user_side_doc/help-and-support/ticket-history.md)

---

## List view

### Auctioneer

| Item | Location |
|------|----------|
| Route | `/dashboard/user-support/ticket-history` |
| Component | `Components/UserSupport/TicketHistory/index.jsx` (`TicketHistories`) |
| Data | React Query: `fetchRequestACallbackDetails`, `fetchWriteToUsDetails` |

### Bidder

| Item | Location |
|------|----------|
| Route | `/bidder/support/ticket-history` |
| Component | `Bid_Support/Ticekt_History/index.jsx` |
| Data | `useEffect` + `fetchCallbackSupports` / `fetchEmailSupports` |

### UI

- Toggle: **CALLBACK REQUESTS** | **E-MAIL REQUESTS**
- Columns: S.No, Ticket ID, Subject (`reqName` or `description`), Created on, Status, Last updated on
- Status CSS: `status-raised`, `status-open`, `status-closed`

---

## Detail routes

| Type | Auctioneer route | Bidder route | Data source |
|------|------------------|--------------|-------------|
| Callback | `/dashboard/user-support/ticket-history/callback` | `/bidder/support/ticket-history/callback/[id]` | AE: `location.state.ticket`; Bidder: `POST view/callbackrequest` |
| Email | `/dashboard/user-support/ticket-history/email` | `/bidder/support/ticket-history/email/[id]` | AE: `state.ticket`; Bidder: `POST view/emailrequest` |

Row navigation passes ticket in state (auctioneer) or links to id URL (bidder).

---

## Continuing a conversation

**Email only:**

1. Open ticket from **E-MAIL REQUESTS** tab.
2. Select **REPLY TO ADMIN**.
3. Inline `WriteToUs` with `replyEmail={true}` → `replyToEmailSupport` with ticket `_id`.
4. Thread renders via `MailFormat` / `Mail_Format` from `emailThread` (User vs Administrator).

**Callback:**

- Detail shows admin **`comments`** and original request fields.
- **No reply button** in customer UI.

---

## Status lifecycle

| Status | Typical meaning |
|--------|-----------------|
| **Raised** | User just created ticket |
| **Open** | Email ticket has user reply (backend sets on `replyEmail`) |
| **Closed** | Admin closed (admin panel); policy: user should not reply |

Callback tickets often remain **Raised** until admin updates status.

---

## APIs (history and detail)

| Method | Path | Purpose |
|--------|------|---------|
| POST | `/api/customer/support/view/callbackRequests` | List callbacks for `req.user.id` |
| POST | `/api/customer/support/view/emailRequests` | List email tickets for `req.user.id` |
| POST | `/api/customer/support/view/callbackrequest` | Single callback `{ id }` |
| POST | `/api/customer/support/view/emailrequest` | Single email `{ id }` |

Lists filter by `userID` from JWT — users cannot see other accounts’ tickets.

---

## Shared thread UI

| App | Component |
|-----|-----------|
| Auctioneer | `TicketHistory/MailFormat/index.jsx` |
| Bidder | `Ticekt_History/Mail_Format/index.jsx` |

Message bodies may be HTML (`dangerouslySetInnerHTML`).

---

## Related

- [Callback support](./callback-support.md)
- [Email support](./email-support.md)
- [Fields](./fields.md)
