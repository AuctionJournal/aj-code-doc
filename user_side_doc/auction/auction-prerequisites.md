[Auction](./index.md) · [Auction Journal](../index.md)

# What are the prerequisites for creating an auction?

Before **+ NEW AUCTION** succeeds, Auction Journal checks that your account is ready. If something is missing, you see **Requirements Missing** instead of the create form. Fix each item listed, then try again.

This page is the **auction-specific** checklist. For the full “fully functional auctioneer” path (order of setup, optional templates, profile), see **[Initial setup](../auctioneeer/initial-setup.md)**.

---

## Standard auctions (most types)

For **Online Timed Auction**, **Online Absolute Auction**, and **Onsite With Live Webcast**, you need **all** of the following before the draft is created:

| Requirement | Where to fix it |
|-------------|-----------------|
| **Stripe Connect** — onboarded (details submitted; charges and payouts enabled) | **Miscellaneous → Stripe Connect** — [guide](../auctioneeer/stripe-connect.md) |
| **Invoice details** saved | **Miscellaneous → Invoice Details** — [guide](../auctioneer-misc/invoice-details.md) |
| **At least one seller** (customer/client on your account) | **Customers** — [add a customer](../auctioneer-client/add-customer.md) |
| **Commission** formula | **Miscellaneous → Formulas** (COMMISSION) — [formulas](../auctioneer-misc/formulas.md) |
| **Buyer premium** formula | Formulas (BUYER PREMIUM) |
| **Buyer tax** formula | Formulas (BUYER TAX) |
| **Seller tax** formula | Formulas (SELLER TAX) |

Formulas need an **Account** under **Miscellaneous → Account** if your account list is empty.

---

## Absentee Bidding only

For **Absentee Bidding**, the system only checks:

| Requirement | Required? |
|-------------|-----------|
| **At least one seller (customer)** | Yes |
| Stripe Connect | No (for this gate) |
| Invoice details | No |
| Commission / buyer premium / tax formulas | No (for this gate) |

You can still use formulas and Stripe later for settlement and payments; the **create** gate is lighter so you can open an absentee auction sooner.

---

## What the Requirements Missing dialog shows

If create is blocked, the popup lists only what still fails, for example:

- **Stripe Connect** — create or finish onboarding under Miscellaneous.
- **Missing Invoice** — save invoice details.
- **Missing Sellers** — add at least one seller under Customers.
- **Missing Commission / Buyer Premium / Buyer Tax / Seller Tax** — add the matching formula type.

Close the dialog, complete the items, then open **+ NEW AUCTION** again.

---

## Prerequisites vs publish

Meeting the table above lets you **create a draft**. **Publish** still requires full auction content (description, address, images, bidding rules, lots for catalogued types, etc.). See [create an auction](create-auction.md) and [Upload Settings](build-upload-settings.md).

---

## Related

- [Auction types — which to choose](auction-types.md)
- [Initial setup](../auctioneeer/initial-setup.md)
- [Formulas](../auctioneer-misc/formulas.md)
- [Help and Support](../help-and-support/index.md)
