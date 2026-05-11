[Auction Journal](../index.md)

# Help And Support Fields

Source models:
- `AJ-Main-Backend/app/models/writeToUs.js`
- `AJ-Main-Backend/app/models/requestACallBack.js`

## Common Ticket Fields

- `TicketID`: business ticket identifier
  - email support format: `WTUS_*`
  - callback support format: `RAC_*`
- `userID`: ticket owner reference
- `userType`: `Bidder` or `Auctioneer`
- `Status`: `Raised`, `Open`, `Closed`
- `comments`: comment thread entries (admin/backend support use)
- `createdAt`, `updatedAt`: timestamps

Business meaning:
- Provides shared lifecycle and ownership model for both support channels.

## Email Support Fields (`writeToUs`)

- `name`: requester display name
- `email`: requester email
- `description`: short issue summary/topic
- `message`: primary support message
- `attachment`: array of attachment URLs/links
- `priority`: severity label (business uses High/Medium/Low)
- `emailThread`: structured conversation timeline

### Email Thread Item Fields

- `name`: sender display name
- `email`: sender email
- `message`: thread message body
- `description`: context/subject line for that reply
- `attachment`: attachment URLs/links for that thread item
- `senderType`: `User` or `Administrator`

Business meaning:
- `writeToUs` stores full conversational support history, including who sent each message.

## Callback Support Fields (`requestACallBack`)

- `reqName`: callback request title/reason label
- `desc`: callback context/description
- `callbackDateTime`: user preferred callback time
- `firstName`, `lastName`: requester name breakdown
- `phoneNumber`: callback contact number
- `email`: requester email

Business meaning:
- `requestACallBack` captures contact intent and scheduling preference for support call-back handling.

## Status Semantics

- `Raised`: ticket created by user
- `Open`: active handling/follow-up
- `Closed`: manually closed by admin

Policy context from business docs:
- Closure is admin-driven and discretionary.
- Closed tickets are read-only for users.
