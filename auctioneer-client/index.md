[Auction Journal](../index.md)

# Auctioneer Client

This module documents auctioneer client business logic across `AJ-Main-Backend` and `auctioneer_dashboard_revamp`.

## Business Purpose (Interview)

- Auctioneer Client acts as auctioneer-owned CRM profile used for buyer/seller/floor-bidder operations.
- A client can represent:
  - seller/consigner
  - floor bidder
  - bidder-linked buyer profile
- A single client profile can hold multiple roles simultaneously based on business use.
- Business rule: when a bidder registers in any auction of an auctioneer, that bidder becomes/updates as that auctioneer's client profile.

## Backend-Aligned Baseline

- Core data is stored in `clientsOfAuctioneer` model.
- System supports both:
  - manual client creation by auctioneer
  - invite + self-register flow for client onboarding
  - auto create/update from bidder linkage flows
- Client profile includes identity, contact, accounting, bid-permission, and bidder-link metadata.
- Bidder-link conversion follows exact update behavior (no loose/ambiguous merge rule).
- If the profile was a floor bidder, conversion to bidder-linked client sets floor-bidder state to `false`.
- On conversion, accounting data should be overwritten from bidder profile context.
- Invite/self-register onboarding should default client role coverage to both buyer and seller.
- Auctioneer can edit bidder-linked client fields in auctioneer-client context.
- Sync direction rule:
  - bidder profile edits propagate to bidder-related client records across auctioneers
  - auctioneer-client edits do not write back to bidder profile (no reverse sync)
- Bid-permission note visibility rule:
  - private notes are visible only to the same auctioneer
  - public notes can be seen by other auctioneers for shared bidder-client context

## Current Enforced Required Fields (Implementation-Aligned)

This section reflects current implementation behavior across backend + revamp UI, not final business policy.

### UI-Enforced Required Fields (`auctioneer_dashboard_revamp`)

Common required in Build Client and Build Floor Bidder flows:
- `clientCode`
- `firstName`
- `lastName`
- `clientMail` (valid email format)
- `phone1`
- billing address (`billTo`):
  - `address1`
  - `zip`
  - `city`
  - `state`
  - `country`

Conditional:
- `shipTo.address1` required when shipping address is different from billing address.

Invite self-join flow also requires:
- `dlNumber`

### Backend-Enforced Notes (`AJ-Main-Backend`)

Model-level required fields in `clientsOfAuctioneer`:
- `clientCode`
- `auctioneer`
- `clientMail`

Controller-level protections in add/update flows include:
- duplicate prevention by `clientMail` / `buyerRef` / reserved bid card
- duplicate driving-license prevention when `dlNumber` is provided
- floor-bidder path constraints (email handling and bidder-account conflict check)

## Dashboard Alignment (`auctioneer_dashboard_revamp`)

- Client module surface includes build/view/edit flows and accounting sections.
- Client operations are grouped under dashboard customer/client areas.
- Manual add flow (routes, APIs, types): [Building / adding a client](./build.md)
- All creation paths: [Ways customers are created](./creation-paths.md)
- Bulk import / export: [Import and export customers (CSV)](./import-export.md)
- Buyer / consigner preferences: [Accounting preferences](./accounting-preferences.md)
- Customer bid permission: [Bid permission](./bid-permission.md)
- User steps: [Who is a customer? How do I add one?](../user_side_doc/auctioneer-client/add-customer.md)
- Mailing lists: [Client Mailing List](./mailing-list.md) — APIs, model, website subscribe list
- Bidder linkage: [Bidder vs client](./bidder-relationship.md) — auto-create on `POST /api/auction/register`
- Floor bidder: [Floor bidder](./floor-bidder.md) — client create + auction check-in

## Field Reference

- Detailed client model fields: [Fields](./fields.md)

## Pending Interview Clarifications

- Exact role semantics: seller vs buyer vs floor bidder vs linked bidder precedence.
- Required fields for each client type at create time.
- Edit restrictions after client is linked to bidder record.
- Bid-permission lifecycle business rules and visibility.
- Additional business implications after floor-bidder-to-bidder conversion are pending discussion/implementation.
