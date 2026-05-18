[Auction Journal](../index.md)

# Auctioneer Miscellaneous

This module groups operational settings used by auctioneers for supporting business tasks outside core auction/listing build flows.

## Business Purpose

- Provide a central operational settings area for auctioneers.
- Support setup and maintenance tasks needed before and during business operations.
- Keep financial, policy, and integration configuration separate from primary auction/listing workflows.
- Configuration can be completed in any order based on auctioneer preference.

## Confirmed Scope (Interview)

- Invoice Details — `/dashboard/miscellaneous/invoices`; [Invoice details](invoice-details.md)
- Auction Locations
- Formula Management (commission, buyer premium, taxes) — see [Formulas](formulas.md)
- Account Management (parent/sub accounts)
- Stripe Connection
- Templates / Text Configuration — see [Templates](templates.md):
  - Terms & Conditions
  - Short BP Explanation
  - Bidding Notices
  - Auction Notices
  - Email Body
  - Bid Increment

Scope interpretation:
- Formulas and accounts are auction-use utility configurations.
- Formula management supports multiple formulas per formula type (for example, slab/range-based variants).
- Stripe Connect is configured from this hub; **auction-creation eligibility** (including completed Connect) is enforced in auction build — see [Stripe Connect](../payment/connect-account.md) and [Auction build](../auction/build.md).
- Stripe Connect is treated as a miscellaneous setup item in this document context.
- Template items are also part of miscellaneous scope in dashboard experience.
- Template items are editable reusable text blocks for communication/compliance use.
- Template values are global at auctioneer-account level (not per-auction variants).

## Product Surface Alignment

From `auctioneer_dashboard_revamp`, the Miscellaneous UI currently includes:
- Account — route `/dashboard/miscellaneous/account`; see [Miscellaneous accounts](account.md)
- Formulas — `/dashboard/miscellaneous/formulas`; [Formulas](formulas.md)
- Stripe Connect — route `/dashboard/miscellaneous/payment/setup`; see [Stripe Connect](../payment/connect-account.md)
- Invoice Details — `/dashboard/miscellaneous/invoices`; [Invoice details](invoice-details.md)
- Templates — `/dashboard/miscellaneous/templates/*`; [Templates](templates.md) (terms, notices, email body, bid increment)

From `AJ-Main-Backend`, active miscellaneous-related API groups include:
- `/api/auctioneer/invoice*`
- `/api/miscellaneous/location*`
- `/api/miscellaneous/formula*`
- `/api/miscellaneous/account*`
- `/api/stripe/connect/*`
- `/api/auctioneer/template*` — auctioneer text and bid-increment templates; see [Templates](templates.md)

## Known Boundary

- This doc treats "Auctioneer Miscellaneous" as an operational setup domain.
- Auction creation and listing creation flows may consume these settings, but are documented in their own modules.

## Pending Interview Clarifications

- Edit restrictions and role permissions per miscellaneous sub-domain.
