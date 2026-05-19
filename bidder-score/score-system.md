[Auction Journal](../index.md) · [Bidder Score](index.md)

# Bidder score — how it works (implementation)

Event-driven trust score for **bidders**, stored on `Bidder.scoreObtained` (-100…100) with an audit trail in `bidderScore`. Auctioneers see the **number** on registration; bidders see **full history** in the dashboard.

**User guide:** [Bidder score — what it is, view, improve](../user_side_doc/bidder-score/score.md)

---

## Code locations

| Layer | Path |
|-------|------|
| Score events | `AJ-Main-Backend/app/controllers/bidder/score/create.js` → `createBidderScore`, `createBidderScoreQueue` |
| Score logs API | `AJ-Main-Backend/app/controllers/bidder/score/index.js` → `fetchBidderScoreLogs` |
| Reasons / point values | `AJ-Main-Backend/app/constant/variables.js` → `bidderScoringReasons` |
| Models | `Bidder` (`scoreObtained`, `latestScoreObtained`), `bidderScore` (timeline) |
| Bidder UI | `auctionjournal-public` → `/bidder/score-history` → `Components/Bidder/Score_History` |
| Layout (current score) | `Components/Bidder/Bidder_Layout` — header shows `bidder.scoreObtained` |
| API client | `lib/api/bidder/profile.js` → `fetchScoreLogs` → `POST /api/bidder/score/logs` |

---

## API

| Method | Path | Auth | Handler |
|--------|------|------|---------|
| `POST` | `/api/bidder/score/logs` | Bidder JWT | `fetchBidderScoreLogs` |

Body: `{ page, limit }` (optional). Response: `scoreLogs`, `totalScore`, pagination fields. Logs exclude `isCanceled: true`. Populates `availableReference` for auctioneer name and `AuctionID` when `referenceRelation` is `auctionsOfBidder`.

Score updates are **not** exposed as a single public “add points” API; callers invoke `createBidderScore(reason, bidderId, bidder, furtherInfo)` or queue `BIDDER_SCORE_UPDATE` with `createBidderScoreQueue`.

---

## `createBidderScore` reasons (switch)

| Reason key | Typical trigger |
|------------|-----------------|
| `bidderSignup` | Bidder registration |
| `verifyingAccount` | Verification completed |
| `fifteenAuctionRegistration` | Every 15th `AuctionRegistration` count |
| `payingFiveBids` / `payingAuction` | Settlement payment milestones |
| `noPaymentAtSettelment` | Non-payment at settlement |
| `noShowForLotPickup` | Pickup no-show |
| `chargebackAtSettelment` | Chargeback |
| `registrationPermissionChange` | Auction registration declined/approved (per auction) |
| `clientBidPermissionChange` | Auctioneer client bid permission decline/approve |

Decline scores differ for **non-payment** vs **other** reasons; permanent vs single-auction strings in `bidderScoringReasons`. Reversible declines set `isCanceled` when later approved.

---

## Registration interaction

`auctionOperations/registration/index.js` → `createAuctionRegistration`: if `buyerPermissionStatus` is `Default` and `bidder.scoreObtained < 0`, registration `bidPermissionStatus` may be **Pending** instead of **Approved**.

---

## Field reference

[fields.md](./fields.md) · Reason point table in [index.md](./index.md#scoring-values-current-reference)

---

## Related

- [Bidder verification](../bidder/verification.md) (user) — `verifyingAccount` score  
- [Bidder vs client](../auctioneer-client/bidder-relationship.md) — `clientBidPermissionChange`
