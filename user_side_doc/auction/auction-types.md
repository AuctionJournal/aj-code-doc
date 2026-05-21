[Auction](./index.md) · [Auction Journal](../index.md)

# What types of auction exist? Which one should I choose?

When you click **+ NEW AUCTION**, the first required choice is **Auction Type**. You pick **one** type in the **New Auction** window. That type **cannot be changed** later on the build screen.

![New Auction — select auction type](../image/auction/new-auction-modal.png "Auction Type")

After you save, you fill [Details](build-details.md), [Upload Settings](build-upload-settings.md), lots, and other tabs. The type controls which bidding fields, lot rules, and publish checks apply.

---

## The four auction types (CRM)

| Auction type | In short |
|--------------|----------|
| **Online Timed Auction** | Bidders bid **online** during an open-to-close window; optional **soft close** extends lots near the end. |
| **Online Absolute Auction** | **Online** sale; items sell **absolute** (no reserve) — highest bidder wins. |
| **Absentee Bidding** | Bidders submit **absentee** bids ahead of the sale; different setup and **lighter** account requirements. |
| **Onsite With Live Webcast** | **In-person** sale plus **live webcast**; choose **catalogued** or **non-catalogued** lots at create; supports rings, pre-bidding, and bidding days. |

These four names are what you see under **Auctions → + NEW AUCTION**. They are **not** the same labels as public **listing** types (five options under Listings). A listing **promotes** an event; an auction **runs** it in your dashboard. See [listing types](../listing/listing-types.md) vs [create an auction](create-auction.md).

---

## Which type should I choose?

### Choose **Online Timed Auction** when

- Bidding runs on Auction Journal over a **defined online period** (open bidding → close bidding).
- You want **timed** closing with optional **soft close** on lots.
- You will catalog **lots** in the CRM and need commission/buyer premium at publish.

Typical **Upload Settings**: timezone, open/close bidding, bid increments, verified or registered bidders.

### Choose **Online Absolute Auction** when

- The sale is **online** and hammer price is **absolute** (no reserve on lots).
- Bidding and registration setup is similar to timed online, with absolute bidding rules.

### Choose **Absentee Bidding** when

- Bidders place **absentee** bids rather than competing in a live timed floor or webcast stream in the same way as timed online.
- You only need **at least one seller (customer)** on your account to create the auction — **not** the full Stripe, invoice, and four-formula checklist (see [prerequisites](auction-prerequisites.md)).

Publish rules differ (for example commission/buyer premium are not required the same way in backend validation).

### Choose **Onsite With Live Webcast** when

- You run a sale **on site** and also stream or webcast to remote bidders.
- At create you must pick **lot type**:
  - **Catalogued** — normal lot catalog; publish usually requires complete lots.
  - **Non Catalogued** — flat bidding; stricter seller/commission rules at publish without a full catalog.
- Extra sections appear later: **Ring Option** (multi-ring), pre-bidding, **bidding day** timings, and onsite-specific bidding in [Upload Settings](build-upload-settings.md).

For ring behavior, see [How does Ring work in an auction?](rings.md). For onsite detail, see [Onsite live webcast](onsite-livewebcast/index.md).

---

## What is fixed after you create?

| Field | After draft is created |
|-------|-------------------------|
| **Auction Type** | Locked |
| **Lot type** (Onsite only) | Locked |
| **Name**, **listing date**, most other fields | Editable on draft; some lock after publish / listing date — see [create auction](create-auction.md) |

---

## Listing on the public site

Creating an auction in the CRM does **not** automatically create a public **listing**. To advertise on Auction Journal’s website, also [create a listing](../listing/create-listing.md) and pick the listing type that matches how you promote the event.

---

## Related

- [Prerequisites for creating an auction](auction-prerequisites.md)
- [Create an auction](create-auction.md)
- [Upload Settings](build-upload-settings.md)
- [Initial setup — fully functional auctioneer](../auctioneeer/initial-setup.md)
