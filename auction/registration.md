[Auction Journal](../index.md)

# Auction registration

## Business purpose

**Auction registration** is how a **bidder** (or floor participant linked to a **client**) is tied to a **specific auction** under that auction’s **auctioneer**, with **permission to bid** and related rules. This page covers **online** and **onsite live webcast floor** registration, **auctioneer permission changes**, and **auctioneer list tools** (export, broadcast email, invite bidder) at a business level. **Screen-by-screen** auctioneer UI is not documented here.

Persisted fields for the registration row: **[Registration fields](./registration-fields.md)**.

---

## Online bidder (self-serve) — core flow

1. The auction is **published** and the bidder is within the auction’s **registration window** (from **`startDate`** through **`endDate`** inclusive in practice: outside that window, self-serve registration is not offered).
2. The bidder meets the auction’s **registration tier** set by the auctioneer (**verified bidder** vs **registered bidder** in product terms). The platform checks the bidder’s verification state accordingly before accepting registration.
3. **Self-serve registration** can create **only one** registration row per bidder per auction. If a row already exists (any status), another online registration is **not** allowed—the auctioneer must act if the situation should change.
4. The system ensures an **auctioneer client** record exists for that bidder under that auctioneer (creating or updating as needed) so accounting and permissions have a single place to attach.
5. If the bidder supplies a **payment method** for registration, it is validated as usable; *(see Pending below if a card is always mandatory.)*
6. A **bid card number** is chosen: reuse a reserved number on the client when present; otherwise allocate the next free number from the auction’s starting bid card sequence, skipping numbers already taken on that auction or reserved on other clients of the same auctioneer.
7. **Bid permission status** and the **bidder permission** (bid cap) are derived from the client’s prior standing with that auctioneer, any **invitation** that granted a cap, the bidder’s **score** at registration time, and the auction’s default cap—so the bidder may land in **pending manual review**, **approved**, or **declined** (the system also stores stronger “permanent” variants from the client record; this doc treats them all as **declined** for explanation).
8. A **registration record** is stored, a **bidder score** update may be queued (milestone-based), and an **email** is sent that matches the outcome (pending vs approved vs declined).

---

## Key rules (online bidder)

- **Window:** Self-serve registration only when the current time is **on or after the auction `startDate`** and **before or on the auction `endDate`** (aligned with [Auction build](./build.md): public visibility and registration open from `startDate`).
- **One registration row per bidder per auction:** After the first online registration is created (**any** status—pending, approved, declined, or permanent variants), the bidder **cannot** submit another self-serve registration for that auction. Further changes are **auctioneer permission and registration updates** (see **Permission changes (auctioneer)** below).
- **Client of auctioneer:** Every online registration ties to a **client** row for that auctioneer; the bidder does not remain “anonymous” to the auctioneer’s client list.
- **Outcome is not always “approved”:** Low bidder score or auctioneer history can leave the bidder in **pending** until the auctioneer acts, or **declined** with messaging. Finer **declined** variants in data (including “permanent”) are **not split out** on this page.
- **`Pending` resolution:** Nothing updates a **Pending** registration **automatically**. Only the **auctioneer** may **approve** or **decline**; **assistants** are not intended to perform this decision in product design.
- **After approval:** Once the registration is **Approved**, there is **no separate “unlock bidding”** step for that auction—the bidder **may bid when bidding is open** for that auction and lot, subject to normal bidding rules (timing, caps, lot state, etc.).

---

## Floor bidder (onsite live webcast only)

**Who drives it:** The **auctioneer** (not a self-serve bidder action).

**When it applies:** Only for auctions of type **Onsite with live webcast**, and only while the auction is in the same **`startDate`–`endDate`** window used for other registration.

**What “floor bidder” means here:** The auctioneer registers a **floor participant** against an existing **auctioneer client** so that person can participate in the **in-room / ring-side** flow for that live webcast auction. The system stores a normal **auction registration** row, marked as a **floor bidder**, with **Approved** permission for that onboard path, a **bid card** chosen with the same collision-avoidance idea as online (reserved card on the client when set; otherwise next free number for the auction among used and reserved numbers), and notes carried from the client’s buyer-permission messaging where present.

**Inviting instead of immediate onboard:** In the same overall feature, the auctioneer may instead send mail to bring in an **existing bidder** or someone who **will sign up as a bidder**—those branches **prepare** the person and **do not** create the floor registration row in that same step.

**Live webcast readiness:** After a floor client is successfully onboarded, the platform turns on **floor bidder availability** for that auction’s live webcast setup so the event can use floor bidding.

More on the live event: [Onsite live webcast](./onsite-livewebcast/index.md).

---

## Floor vs online bidder (identity)

- **Different account paths:** A **floor** registration is anchored on the **auctioneer client** (floor participant) path; an **online** registration is anchored on a **bidder** account. They are **not** treated as interchangeable identities on the same auction—settlement and permissions follow **the registration row** that applies, not an assumed “same person” merge across both.
- **Target rule (not both at once):** When someone **moves to an online bidder** for that auction, they **should not** remain an active **floor bidder** for the same participation story—the business treats those roles as **mutually exclusive**.
- **Implementation:** A dedicated auctioneer **floor → online bidder** conversion (and automatic **cleanup** of floor-only state—live webcast floor flags, bid card handling, etc.) is **not completed yet**. Until that ships, do not assume the platform enforces the target rule end-to-end; operations may need **manual** steps. See **Pending clarifications**.

---

## Permission changes (auctioneer)

The **auctioneer** may update an existing **auction registration**—for example resolving **pending** into **approved** or **declined**, changing the **bid cap**, or updating **notes**, **decline reasons**, and **contact-sharing** choices visible to the bidder. The platform sends **email** to the bidder when outcomes change, and may record **bidder score** events where trust history should reflect the decision. When a change is meant to **stick for the buyer across that auctioneer’s relationship**, the same action can also align the **auctioneer client** buyer-permission fields so listings and future registrations see a consistent story. Internal implementation is treated as **one “permission change”** product idea, not multiple separate flows in this doc.

---

## Auctioneer tools (registrations for one auction)

These actions assume the auctioneer is working in context of a **single auction’s registrant list** (authorization is auctioneer-facing; the auction itself should be one they run).

### Export registrations

- Produces a **downloadable spreadsheet (CSV)** of **every registration row** for that auction.
- Typical columns include **bid card**, **name** (from bidder profile or client), **email**, **phone**, **address**, **bidder score** (where available), a **human-readable permission line** (approved / pending / declined and cap, including permanent-style wording where stored that way), **IP captured at registration**, a **terms** column in the file layout, and the **bidder’s note to auctioneer**.
- **Purpose:** operations, check-in, or offline review—not a live API for integrations.

### Email all registrants

- Sends **one campaign** to **all current registrants** of that auction: the auctioneer supplies **subject** and **body**, and the platform resolves **recipient addresses** from each row (bidder email, or client email fields when the bidder path is empty).
- **Purpose:** announcements, reminders, or instructions to everyone registered so far. Recipients are **everyone on the list** for that auction in one send (no per-row filtering described here).

### Invite bidder(s) to register

- The auctioneer chooses a **bid permission amount** (the cap offered with the invite) and either **one bidder by email** (must match an **existing bidder account**) or **multiple bidders** by selection.
- **Not allowed** after the auction’s **`endDate`** (auction treated as closed for this action).
- Bidders **already registered** for that auction **cannot** be invited again on this path.
- For each invited bidder who is not already on file for that auction, the system records an **invitation** tied to the auction and the offered **bid permission**, then sends the **invite email** so they can complete **self-serve registration** (which then applies the usual registration rules, including invitation-sourced cap where applicable—see online flow step 7).
- **Purpose:** proactive outreach to known bidders; it **does not** by itself create a registration row until the bidder registers (except any pre-registration records used only to remember the invite and cap).

---

## Out of scope (for now)

- **Screen-by-screen auctioneer UI** (exact clicks and layouts).
- **Email template copy** and deliverability configuration.

---

## Pending clarifications

- **Payment method:** Confirm whether an **online** registration **must** always include an active card, or only when the UI collects one (implementation only validates when an id is sent).
- **Floor → online bidder:** When the conversion flow is built, document whether **floor-only state** is cleared **in the same action** as conversion or left for **manual** follow-up; update § **Floor vs online bidder (identity)** accordingly.
