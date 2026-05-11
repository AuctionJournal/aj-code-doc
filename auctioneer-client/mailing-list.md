[Auction Journal](../index.md)

# Auctioneer Client Mailing List

This document covers mailing list business logic under Auctioneer Client.

## Business Purpose

- Allow auctioneers to group clients/emails into reusable communication lists.
- Support list-based outreach workflows from dashboard operations.
- Maintain both known-client membership and raw email subscriptions from website flows.

## Core List Capabilities

- Create mailing list (`name`, `description`)
- Edit list metadata and membership
- View one list with populated client details
- View all lists for auctioneer
- Delete list (except protected system list)

## Membership Model

A mailing list can contain:
- `clients`: references to `clientsOfAuctioneer` records
- `emails`: raw email entries (used especially for website subscriptions)

Membership operations support:
- add single client
- add all auctioneer clients
- remove single client
- remove all clients
- sync membership based on latest selected list set

## Special System List

- `Subscribed From Website` list is auto-managed.
- It is identified with `isSubScribedFromWebsiteML = true`.
- Backend prevents deleting this protected system list.

## Website Subscription Join Flow

- Endpoint accepts `email` + `auctioneer`.
- If system list does not exist, it is auto-created.
- If matching client exists, client id is linked.
- Otherwise raw email is stored in `emails`.
- Duplicate subscription attempts are blocked.

## Ownership and Scope

- Mailing lists are auctioneer-scoped.
- List fetch and mutation operate under auctioneer context.
- Client membership is tied to `clientsOfAuctioneer` records.

## Data Model (Business Meaning)

Primary model: `clientMailingList` (`app/models/clientMailingList.js`)

- `name`: mailing list name
- `auctioneer`: owner reference
- `description`: list description
- `clients`: client references
- `emails`: non-client email subscribers
- `isSubScribedFromWebsiteML`: marks system-managed website-subscription list

## Minimal API Map (Reference Only)

- create/edit/delete list
- fetch one/fetch all lists
- add/remove clients in list
- website subscription join to mailing list
