[Auction Journal](../index.md) · [Advertisement](./index.md)

# When does my paid advertisement go live on Auction Journal?

## Business rule (documented)

Paid advertisements go **live on the public website after admin approval**, within the **start and end dates** chosen in the wizard. Payment alone does not mean immediate public display for end users.

## Technical flow (code)

| Stage | What happens |
|-------|----------------|
| Create | `createAdProcess` → record with `isPublished: false` |
| Checkout | Auctioneer pays via `/dashboard/billings/payment/checkout` |
| Payment callback | `updateDetailsAfterPayment` may set `isPublished: true` in backend |
| Public fetch | `auctionjournal-public` loads ads where `isPublished: true` and current date in range |

**Ops note:** If operations require manual review before public display, treat **admin approval** as the gate for “live” in support docs even when the automated flag flips on payment. Align backend/process if product wants strict approval-before-publish.

## Success UI

Dashboard success copy indicates the **request was submitted** with planned run dates (not “now live on homepage”).

## Date window

Ads only surface publicly when **today** is between `startDate` and `endDate` (and scope flags match page).

## Related

- [How ads work](how-it-works.md)
- [Create advertisement](create-advertisement.md)
- [Public placement](public-placement.md)

User guide: [`user_side_doc/advertisement/go-live.md`](../user_side_doc/advertisement/go-live.md)
