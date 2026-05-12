[Auction Journal](../index.md)

# Auction Building

## Business purpose

An auction is the container for lots, fees, registration, and (by type) timed bidding or live webcast behavior. **Building** is the auctioneer workflow to create that container, refine it as a draft, publish it when ready, and adjust it after publish where the product allows.

Persisted header attributes are documented in **[Auction fields](./fields.md)**.

## Auctioneer eligibility (before first draft)

Before an auctioneer can create the **first draft** of an auction, a **server-side eligibility check** runs. Rules **depend on the auction type** being created.

### Absentee Bidding

- At least one **seller** (an auctioneer client exists for this auctioneer).

### All other auction types

Online Timed Auction, Online Absolute Auction, and Onsite With Live Webcast require **all** of the following:

- At least one **seller** (same as above).
- **[Stripe Connect](../README.md)** in a usable state: a connected account exists and Stripe reports `details_submitted`, `charges_enabled`, and `payouts_enabled`.
- At least one **auctioneer invoice** record on file.
- At least one saved **formula** for each of: **commission**, **buyer premium**, **buyer tax**, and **seller tax**.

If any required item is missing, draft creation fails and the client receives flags indicating which checks failed.

## Initial create

- **Auction name** is required.
- **Auction type** is required at creation.
- **Start date** is required at creation.
- For **Onsite with live webcast**, **lot type** is required at creation.

How “locked” fields behave after that is guided mainly by the **auctioneer build experience** (draft vs published vs later phases), not only by what a raw draft save could accept.

## Draft

- The auctioneer fills details, upload settings, lots, and so on, and **saves a draft** until ready to publish.
- The server still validates and may reject some changes at publish time even if they were saved on a draft.

### What the auctioneer can change (dashboard)

- For a **draft**, **auction type** and **lot type** are fixed in the build UI once the auction exists; other details stay editable until the auction progresses.
- After **publish**, more fields become read-only over time as **registration opens**, **bidding opens**, and the auction moves toward **close**—including different rules for **onsite live webcast** auctions.
- While **save**, **publish**, or **delete** is running, those actions are briefly unavailable so the same operation is not submitted twice (this is separate from long-term field locking).

### Pending clarifications

- **Start date:** Confirm whether start date should be treated as **fixed from initial create** in messaging and training; the build UI’s locking rules may not match that story in every phase.

## Delete

**Business rule**

- Only **draft** (unpublished) auctions can be deleted.

**Effects** (when delete is allowed)

- Auction images in storage are removed permanently.
- All lots for that auction are removed, including their images.
- Related auction expense records are removed.

### Implementation vs policy (pending)

- The server’s delete path may still allow removal in edge cases that are not “draft-only.” **Product intent** here is drafts-only; confirm whether the service should be tightened to match.

## Publish

**What publish means**

- **Publish** is the auctioneer’s commitment that the auction is **ready to run** under the configured rules (content, fees, schedule where applicable, and other required setup the product enforces at publish time).
- The auction is **not** shown on the **public** catalog or listing experience **until `startDate`**. From **`startDate`** onward it is **visible to the public**, and **bidders can register** for that auction (see [Registration](./registration.md) for how registration and approval work).

## Edit publish

The **auctioneer may** change **only what the product still allows** for that auction’s **phase** (draft vs published before public start vs after `startDate`, registration open, bidding, close, and onsite-specific timing). As the auction advances, more values become **read-only** so bidders and operations see a stable contract; the build UI reflects those limits without listing every field here.
