[Auction Journal](../index.md) · [Auction Lot](./index.md)

# How do I create a lot in an auction?

---

## Where you create lots

1. Open the auction in the **Auctioneer Dashboard** ([create an auction](../auction/create-auction.md)).
2. Go to the **Lots** tab on the build screen.
3. Click **New Lot**.

The **New Lot** dialog is the manual, one-at-a-time path. Other ways to add lots (import CSV, QR placeholders, mass images, assistant fast catalog) are covered in [sample questions](../sample_questions.md) under **Auction Lot** (questions 2–5).

**New Lot** may be disabled after publish when the auction phase locks lot creation (for example during or after certain bidding windows on online auctions).

---

## Before you click New Lot

The dashboard checks that the auction is ready to accept lots.

| Requirement | Why |
|-------------|-----|
| **Auction ID** set | Identifies the sale in exports and public catalog |
| **Bid increment** configured | Sets starting bid default for new lots |
| **New Lot Default** saved on the auction | Commission, buyer premium, default seller, reserve, taxes — see [Details → New Lot Default](../auction/build-details.md#new-lot-default) |
| **Soft Close Seconds** and **Bid Soft Closed Seconds** (online timed/absolute) | Copied onto each new lot for closing behavior — see [soft close](../auction/soft-close.md) |
| **Commission / buyer premium** (and taxes if you collect taxes) | Required for most auction types; absentee rules differ — see [auction types](../auction/auction-types.md) |

If you changed **New Lot Default** but did not **save the auction**, you will be asked to save first.

---

## New Lot form sections

### Lot Info

| Field | Required | Notes |
|-------|----------|--------|
| **Lot Number** | Yes | Must be unique in this auction (positive number) |
| **Sale Order** | Yes | Order lots run in the sale; must be unique |
| **Quantity** | Optional | Defaults if omitted |
| **Seller** | Yes | **Catalogued:** search and pick a customer. **Non-catalogued onsite:** pick from sellers added on the auction |
| **Title** | Yes | Shown to bidders |
| **Description** | Yes | Lot detail text |
| **Category** | Yes | Pick from auction lot categories |
| **Category Detail** | Optional | Extra category notes |

New lots inherit **commission, buyer premium, taxes, soft-close times, reserve, and start bid** from the auction’s **New Lot Default** and **bid increment** unless you change them later in **Edit Lot**.

### Lot Value

| Field | Notes |
|-------|--------|
| **Selected value range (min / max)** | Optional pair; if you enter one, enter both, and max must be greater than min |
| **Reserve** | Hidden for **Online Absolute**; optional or from default for other types |
| **Start Bid** | **Required** for **non-catalogued** onsite auctions; otherwise prefilled from first bid increment step |

### Lot Media

Upload one or more images. Images upload to storage tied to the auction and lot number. You can add or change images later when editing the lot.

---

## Save

1. Click **Save**.
2. On success you see **Lot created** and the list refreshes.
3. Click **Cancel** to close without saving.

The lot is saved as **auction ready** (`isAuctionReady`) when required fields are present, so it counts toward **publish** rules on **catalogued** auctions (every real lot must be complete before publish). See [create an auction](../auction/create-auction.md).

---

## Special cases

### QR placeholder at the same lot number

If you used **Generate QR** to create placeholder lots for a number range, creating a full lot with the **same lot number** **replaces** that placeholder with a real catalog lot.

### Onsite With Live Webcast (catalogued) and rings

Lot **lot number** or **sale order** (depending on **Assign Ring By**) must fit a configured **ring range**. If the number is above the last ring’s end, the system may **extend the last ring** to include it. If it does not fit any range, creation fails with a message that the lot will not fit in any auction ring range — adjust [ring options](../auction/rings.md) or lot numbers.

### Duplicate lot number or sale order

- **Lot number already taken** — choose another number or edit/delete the existing lot.
- **Sale order already taken** — choose another sale order.

---

## After creating lots

- **Edit** — open a lot from the list to change accounting, visibility, fees, or media ([edit lot](../sample_questions.md) — doc pending).
- **Publish** — for **catalogued** auctions, all non–QR-only lots must be auction ready; see [create an auction](../auction/create-auction.md).
- **Register bidders** and run the sale from the [auction dashboard](../auction/auction-dashboard.md) when live.

---

## Other ways to add lots (overview)

| Method | When to use |
|--------|-------------|
| **New Lot** (this page) | One lot at a time with full details |
| **Import LOTS** | Spreadsheet / CSV bulk |
| **Generate QR** | Placeholder lot numbers before full data (draft, before listing date) |
| **Mass import images** | Upload images keyed by lot number |
| **Instant lot** | During live onsite bidding (separate flow) |

---

## Related

- [New Lot Default](../auction/build-details.md#new-lot-default)
- [Auction types](../auction/auction-types.md)
- [Rings](../auction/rings.md) (onsite catalogued)
- Dev reference (all creation paths): [Auction lot build](../../auction-lot/build.md)
