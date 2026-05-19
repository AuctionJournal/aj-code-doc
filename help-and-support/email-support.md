[Auction Journal](../index.md) · [Help And Support](./index.md)

# Email support (Write to Us)

Users submit **email-style support tickets** with priority, rich message body, and optional attachments. Tickets use prefix **`WTUS_`**, maintain an **`emailThread`**, and support user **replies** that set status to **`Open`**.

**User guide:** [Email support](../user_side_doc/help-and-support/email-support.md)

---

## Frontend

### Auctioneer

| Item | Location |
|------|----------|
| Hub → Write to Us | `Components/UserSupport/index.jsx` → navigate `/dashboard/user-support/write-to-us` |
| Form | `Components/UserSupport/WriteToUs/index.jsx` |
| Reply from history | Same component with `replyEmail={true}`, heading **WRITE TO ADMIN** |
| API | `createEmailRequest`, `replyToEmailSupport` in `lib/api/support/index.js` |

### Bidder

| Item | Location |
|------|----------|
| Route | `/bidder/support/write-to-us` |
| Form | `Components/Bidder/Bid_Support/Write-to-us/index.jsx` |
| API | `lib/api/support/email.js` (`requestEmailSupport`, `replyToEmailSupport`) |

---

## Form modes

| Mode | When | API |
|------|------|-----|
| **New ticket** | Write to Us page from hub | `POST .../emailRequest` |
| **Reply** | Email ticket detail → **REPLY TO ADMIN** | `POST .../replyEmail` (body includes Mongo `id`) |

### Fields (create and reply)

| Field | UI | Validation / notes |
|-------|-----|-------------------|
| `name` | Read-only on create | From logged-in user |
| `email` | Read-only on create | Lowercase enforced in yup (auctioneer) |
| `priority` | Low / Medium / High | Required; backend enum |
| `description` | Short summary (labeled *Description*; yup may say “Subject”) | Required |
| `message` | Rich text (`TextEditor`, HTML) | Required |
| `attachment` | Optional file(s) | Uploaded to S3 before API call |

### Attachment upload

| App | S3 blob type | Client |
|-----|--------------|--------|
| Auctioneer | `auctioneerSupport` | `uploadS3Blob` in `lib/api/blob/index.js` |
| Bidder | `bidderSupport` | Bidder write-to-us upload flow |

Payload sends `attachment` as **array of URLs** after upload.

---

## API

| Method | Path | Handler |
|--------|------|---------|
| POST | `/api/customer/support/emailRequest` | `writeToUs` |
| POST | `/api/customer/support/replyEmail` | `replyToEmailSupport` |
| POST | `/api/customer/support/view/emailRequests` | `getWriteToUsDetails` |
| POST | `/api/customer/support/view/emailrequest` | `getEmailRequestDetails` (`{ id }`) |

**Create validation:** `validateWriteToUsData` — `email`, `name`, `message`, `description`, `priority` ∈ Low/Medium/High, optional `attachment[]`.

**Reply validation:** `validateReplyToEmail` — `id` (MongoId), `description`, `priority`, `message`, optional `attachment[]`.

**Server behavior** (`email-support.js`):

- Create: `TicketID` = `WTUS_*`, `Status: "Raised"`, first `emailThread` entry with `senderType: "User"`, customer email template type **8**, admin notification.
- Reply: sets `Status: "Open"`, updates `priority`, appends `emailThread` (`senderType` `Administrator` if `req.email` matches `Administrator` model, else `User`), notifies admins.

**Known client quirk:** auctioneer `fetchWriteToUsDetails` URL in `lib/api/support/index.js` includes a **trailing space** after `emailRequests` — backend route has no trailing space; verify in deployment if list fetch fails.

**Bidder single-ticket fetch:** `email.js` calls `view/emailRequest` (capital R); backend route is `view/emailrequest` (lowercase). Confirm casing matches server if detail fetch fails.

---

## Intended policy vs UI

Business policy ([index](./index.md)): **Closed** tickets are read-only for users. The email detail UI still shows **REPLY TO ADMIN** without checking `Status === "Closed"`; backend `replyToEmailSupport` does not block replies on closed tickets either.

---

## Model

`app/models/writeToUs.js` — `emailThread` structure in [Fields](./fields.md).
