[Auction Journal](../index.md) · [Advertisement](./index.md)

# What is the difference between national and state advertisements?

National and state scope is driven by **Package** `behaviour` flags and enforced in create + public fetch logic.

## Flags

| Flag | Meaning |
|------|---------|
| `behaviour.isNationalAd` | Ad competes for **national** featured slots (no state picker in wizard) |
| `behaviour.isStateAd` | Ad is scoped to one **state**; wizard opens `SelectState` before create/checkout |

## Ad types by scope

| Scope | Listing | Business |
|-------|---------|----------|
| **National** | Featured Listing (National) | National Print, National Poster, National Banner, Blogs Ad, Video Ad |
| **State** | Featured Listing (State) | State Poster, State Banner |
| **Listing ticker** | National header ticker (listing content; not state-scoped package) | — |

Ticker uses `listing-ticker-ad` — national site header, not the state featured listing model.

## Dashboard flow difference

State packages: after duration, `AdSelector` → `createAd` checks `selectedPackage.behaviour?.isStateAd` and opens `SelectState` if state missing.

## Public fetch difference

- National featured listings: requests pass `isNationalAd: true`
- State featured listings: `isStateAd: true` and state must match page filter (`statesToShowAd`)

Banner/poster records store the same flags for `fetchBannerAd`.

## Collision rules

Featured listing create checks existing **published** ads for same `AdRef` + national/state scope to reduce overlap.

User guide: [`user_side_doc/advertisement/national-vs-state.md`](../user_side_doc/advertisement/national-vs-state.md)
