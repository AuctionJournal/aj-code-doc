[Auction Journal](../index.md) · [Help And Support](./index.md)

# Live chat (Zoho SalesIQ)

Auction Journal embeds **Zoho SalesIQ** as a **global live-chat widget** on auctioneer and bidder web apps. It is **not** part of the customer support ticket APIs in `helpAndSupport.js` and does **not** create `RAC_*` or `WTUS_*` tickets.

**User guide:** [Live chat](../user_side_doc/help-and-support/live-chat.md) (vendor not named for end users).

---

## Integration points

### Auctioneer dashboard

| Item | Location |
|------|----------|
| Script embed | `auctioneer_dashboard_revamp/public/index.html` — inline `<script id="zsiqchat">` |
| Loader | `https://salesiq.zoho.com/widget` |
| Widget code | Set in `$zoho.salesiq.widgetcode` in that file |

Loaded for the whole SPA; **not** imported from `Components/UserSupport`.

### Bidder / public app

| Item | Location |
|------|----------|
| React wrapper | `auctionjournal-public/src/app/Components/Scripts/index.jsx` — `ZohoSalesIQScript` |
| Mounted from | `Components/Layouts/PrimaryLayout/index.jsx` |
| Loader | `https://salesiq.zohopublic.com/widget` |
| Widget code | Inline in `dangerouslySetInnerHTML` (distinct widgetcode from auctioneer build) |

---

## Behavior vs ticket support

| Aspect | Zoho SalesIQ | Write to Us / Callback |
|--------|--------------|-------------------------|
| Backend route | None in AJ-Main-Backend | `/api/customer/support/*` |
| Ticket History | Not listed | Listed by tab |
| Auth tie-in | Widget session managed by Zoho | JWT-scoped tickets |
| Attachments / priority | Chat product features | Form fields on `writeToUs` |

---

## Do not confuse with

**Live webcast auction chat** (`LiveWebcastChat` and related auction routes) — in-auction bidder/auctioneer messaging during bidding, not AJ customer support.

---

## Operations notes for developers

- Widget availability and hours depend on **Zoho SalesIQ configuration** (outside this repo).
- Different **widgetcode** values between auctioneer and public builds imply separate SalesIQ deployments or brands.
- Changes to chat behavior are made in Zoho admin or embed scripts above, not in `callback-support.js` / `email-support.js`.
