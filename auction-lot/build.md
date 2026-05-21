[Auction Journal](../index.md)

# Auction lot build

## Business purpose

An **auction lot** is a sellable line in an auction’s catalog: numbering, media, description, seller, pricing defaults, and readiness for bidding. **Building** the catalog means creating those lot records and progressing them toward **auction ready** (complete enough to publish and bid under the auction’s rules).

Field-level detail: **[Fields](./fields.md)**. Lot-AI enrichment (not a creation path): **[Lot AI analysis](./build-lot-ai.md)**.

---

## Ways to create or seed the catalog

The product supports several entry points. They can be combined (for example QR placeholders first, then full lot create on the same numbers, or fast catalog then Lot AI).

### 1. Auctioneer — manual lot create

User guide: [How do I create a lot in an auction?](../user_side_doc/auction-lot/create-lot.md).

The auctioneer creates **one lot at a time** with full catalog fields (lot number, sale order, seller, title, category, description, media, reserves, fees inherited or overridden as the product allows, etc.). The server validates the payload and enforces **unique lot number** and **sale order** rules for that auction. For **onsite live webcast** catalogued auctions, lot numbers must fit **ring ranges** unless a dedicated **on-the-fly / instant** create path relaxes some checks for in-event use.

If a **QR-only placeholder** already exists for that lot number, a normal create can **replace** it with a full catalog row (placeholder cleared in favor of the real lot).

| Layer | Path |
|-------|------|
| **UI** | `auctioneer_dashboard_revamp/src/Components/AuctionLot/CreateAuctionLot/` (`LotCreateForm`); opened from **Lots** tab → **New Lot** (`ManageAuctionLots`) |
| **API** | `POST /api/auctioneer/auction/lot/create` → `lotCreation` in `lot/lot-build.js` |
| **Validation** | `validate_LotCreation_Request` (`lot/lot.validation.js`); client `lotSchema` (yup) in `CreateAuctionLot/index.jsx` |
| **Client** | `createAuctionLot` in `src/lib/api/auction/lot.js` |

**Defaults on create:** Payload merges auction **New Lot Default** (commission, BP, taxes, soft close, reserve, start bid from first increment). `getAuctionLotFields` applies seller tax flags. `isAuctionReady: true` on success. **Ring:** `checkAuctionRingAwailability`; may extend last ring or return `ringOptionUnavailabilityError` (UI: `RingOptionUnavailabilityError` modal). **Onsite catalogued:** `ringOptionUpdateOnLotUpdate` after save.

### 2. Assistant — fast catalog (minimal lots)

An **assistant** (scoped to an auctioneer) can add lots with **minimal information**—typically **lot number**, **images**, **quantity**, and optionally **seller**—while the auction has the required header configuration (auction ID, default seller, increments, fees, soft-close settings where applicable, etc.). New rows are created **hidden** from the public catalog and marked **not auction ready** until completed (often via **Lot AI** or auctioneer edit). There is a **single-lot** path and a **batch** path with a prior **validation** step for bulk fast catalog.

### 3. CSV import (auctioneer)

The auctioneer uploads a **CSV** and maps columns to lot fields. The system runs **validation** (sellers, categories, duplicates, sale order, ring fit where relevant) and can **create new lots** and optionally **overwrite** existing rows when the product allows. **Timing rules** apply on **published** timed auctions (for example import blocked very close to close; overwrite may be blocked after bidding has opened). This is the main **spreadsheet-scale** catalog path.

A separate, stricter **auctioneer CSV import** flow exists that only proceeds after an explicit **“approved” validation** flag from the client—same business idea (bulk create/update from file), different guardrails in code.

### 4. QR-only placeholder range (auctioneer “initiate”)

The auctioneer defines a **range of lot numbers**. The system creates **stub lots** for missing numbers in that range, marked **for QR use only** (placeholders for printing labels or field workflow). Numbers already present are skipped. Placeholders **above** the chosen range end can be removed so the range is the single source of truth for how many QR slots exist. Full catalog data is added later by **manual create**, **import**, or **assistant fast catalog** (and placeholders at the same number are superseded). Any **QR-only** rows that are **still left** when the auction **publishes** are **removed** by the platform (they do not go live as catalog lots).

### 5. Mass images by lot number (auctioneer or assistant)

Images can be uploaded **in bulk keyed by lot number**. Where a lot does not exist yet, the flow can **create** lot rows to hold those images (aligned with fast-catalog style defaults and **not auction ready** until finished). This is primarily a **media-first** way to grow the catalog, not a full data import.

---

## Auction ready vs publish

- **Catalogued** auctions: Every **real** lot (everything except **QR-only** placeholders) must be **auction ready** (`isAuctionReady` true) before the auction can **publish** successfully. Incomplete lots block publish until they are finished or removed. **On publish**, any remaining **QR-only** placeholder lots are **deleted**—they are workflow aids before go-live, not part of the published catalog. See also [Auction build](../auction/build.md).
- **Non-catalogued** auctions: Publish is **not** framed around a **complete catalog of ready lots** the same way—**auction-level** setup (fees, sellers, etc.) carries the sale. Lot-level **auction ready** is still the flag used when lots exist and are being filled in, but the **publish gate** you communicate for non-catalogued is the **header / formula / seller** story, not “every line in the catalog.”

### Implementation vs messaging (pending)

- If a **non-catalogued** auction **still has lot rows** in the database, the service may still treat **incomplete** non–QR-only lots as blocking publish. Confirm whether that edge case should match the **customer-facing** rule above.

---

## What this page does not cover yet

- **Edit**, **delete**, **visibility**, **soft close**, **sale order** tools, **export/download** samples—same build area in the app; to be documented in follow-on sections.

---

## Pending clarifications

- Whether product messaging should present **one** “CSV import” story to users or distinguish the **validation-approved** import path from the **flexible mapper** import in training materials.
- Which tools **assistants** are allowed to use beyond **fast catalog** (e.g. mass image upload): **Pending** product confirmation.
