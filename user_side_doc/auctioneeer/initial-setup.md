[Auctioneer](./index.md) · [Auction Journal](../../index.md)

# What initial setup is required to become a fully functional auctioneer?

After you [register](registration.md) and sign in to the **Auctioneer Dashboard**, complete the setup below before you rely on **New Auction**, settlement, and bidder payments. Auction Journal checks these items when you start creating most auctions. If something is missing, you see a **Requirements Missing** message that lists what to fix.

This checklist is for running **full auctions** in the CRM. [Listings](../listing/create-listing.md) on the public site and paying platform fees may also need a [payment card](payment-method.md)—that is separate from the items below.

---

## Required setup (before most new auctions)

Complete every row below. Each has a dedicated guide under **Miscellaneous** or **Customers**.

| # | What to set up | Where in the dashboard | Why it matters |
|---|----------------|------------------------|----------------|
| 1 | **Stripe Connect** | **Miscellaneous → Stripe Connect** | Receive money from bidders; Stripe must be fully onboarded (details submitted, charges and payouts enabled). |
| 2 | **Invoice details** | **Miscellaneous → Invoice Details** | Settlement invoice PDFs and a required check before **New Auction**. |
| 3 | **At least one customer (seller)** | **Customers** (add a **Consigner** or **Buyer and consigner**) | Auction Journal requires at least one **client** on your account—typically a **seller** you will use on lots. |
| 4 | **Commission** formula | **Miscellaneous → Formulas** (type **COMMISSION**) | Seller commission on sold lots. |
| 5 | **Buyer premium** formula | **Miscellaneous → Formulas** (type **BUYER PREMIUM**) | Extra % buyers pay on hammer price. |
| 6 | **Buyer tax** formula | **Miscellaneous → Formulas** (type **BUYER TAX**) | Buyer-side tax rules. |
| 7 | **Seller tax** formula | **Miscellaneous → Formulas** (type **SELLER TAX**) | Seller-side tax rules. |

### Step-by-step links

1. [Stripe Connect — set up and complete onboarding](stripe-connect.md)  
2. [Invoice details — address, receipt title, and options](../auctioneer-misc/invoice-details.md)  
3. [Add a customer (client) — include sellers](../auctioneer-client/add-customer.md) — under **Customers** in the sidebar  
4. [Formulas — buyer premium, buyer tax, seller tax, commission](../auctioneer-misc/formulas.md)  

When you add formulas, each one needs an **Account** from **Miscellaneous → Account**. If the account list is empty, set up [Accounts](../auctioneer-misc/account.md) first, then return to Formulas.

---

## Recommended order

A practical sequence after registration:

```text
Register → sign in → Stripe Connect (create + Add information) →
Invoice Details → Accounts (if needed) → Formulas (commission, BP, taxes) →
Customers (add sellers) → optional Templates → New Auction
```

1. **Stripe Connect** — so payouts are ready before money moves.  
2. **Invoice details** — quick win; blocks **New Auction** until saved.  
3. **Accounts** — only if you need new sub-accounts for your formulas.  
4. **Formulas** — create the four required types (you can add more codes later, e.g. different tax rates).  
5. **Customers** — add sellers (and buyers) you will assign on lots and settlements.  
6. **Templates** (optional) — see below.  
7. Try **Auctions → + NEW AUCTION**. If anything is still missing, the **Requirements Missing** popup names the gap.

---

## Optional but useful setup

| Item | Where | Notes |
|------|--------|--------|
| **Templates** | **Miscellaneous** — Terms, notices, email body, bid increments, etc. | Not required to create an auction. Speeds up repeat sales. See [templates](../auctioneer-misc/templates.md). |
| **Bid permission** on a customer | **Customers** → edit client → buyer settings | Per-customer rule for auto-approve or decline when they register for **your** auctions—not a global “new auction” gate. See [bid permission](../auctioneer-client/bid-permission.md). |
| **Profile** | Account / profile settings | Company name, logo, address on settlements. See [profile](profile.md). |
| **Payment card** | Billing | Pay Auction Journal for listings or other platform charges—not the same as Stripe Connect. See [payment method](payment-method.md). |
| **Import customers** | **Customers** | Many existing clients? See [import clients](../auctioneer-client/import-clients.md). |

---

## How you know setup is complete

When you select **+ NEW AUCTION** (for a standard auction type), Auction Journal should **not** show **Requirements Missing**. The dialog only appears while something below is still incomplete:

| Message in the dialog | What to do |
|----------------------|------------|
| **Stripe Connect** | Finish Connect under Miscellaneous. |
| **Missing Invoice** | Save [invoice details](../auctioneer-misc/invoice-details.md). |
| **Missing Sellers** | Add at least one customer under **Customers** (seller/consigner). |
| **Missing Commission** | Add a **COMMISSION** [formula](../auctioneer-misc/formulas.md). |
| **Missing Buyer Premium** | Add a **BUYER PREMIUM** formula. |
| **Missing Buyer Tax** | Add a **BUYER TAX** formula. |
| **Missing Seller Tax** | Add a **SELLER TAX** formula. |

**Absentee Bidding** auctions are different: the system only requires **at least one customer (seller)** on your account—not the full Stripe, invoice, and formula checklist used for other auction types.

---

## After setup — what you can do

| Area | Next step |
|------|-----------|
| Public marketing | [Create and publish listings](../listing/create-listing.md) |
| Live sales | **New Auction** → build lots, registration, clerking, settlement |
| Customers | [Customer preferences](../auctioneer-client/customer-preferences.md), [mailing lists](../auctioneer-client/mailing-list.md), [floor bidders](../auctioneer-client/floor-bidder/index.md) |

For day-to-day navigation, see [Auctioneer Dashboard](dashboard.md).

---

## Related

- [Who is an auctioneer?](role.md)
- [Auctioneer Miscellaneous hub](../auctioneer-misc/index.md)
- [Auctioneer Clients (Customers)](../auctioneer-client/index.md)
