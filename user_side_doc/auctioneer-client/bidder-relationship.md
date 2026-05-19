[Auctioneer Client](./index.md) · [Auction Journal](../../index.md)

# What is the relationship between a bidder in Auction Journal and a customer of an auctioneer?

**Bidders** and **customers (clients)** are two layers in Auction Journal:

- A **bidder** has **one account** on the public Auction Journal site (login, verification, bidding).
- A **customer** is **your** record of someone in the **Auctioneer Dashboard**—contact details and rules for **your** auctions only.

The same person often has **both**: one bidder account for the whole platform, and **one client profile per auction company** they work with.

---

## How they connect

When someone who is already a **bidder** **registers for one of your auctions**, Auction Journal **automatically** creates or updates a **client** under **your** company. You do not need to add them manually first.

| What happens | Meaning for you |
|--------------|-----------------|
| Bidder completes **auction registration** | A client row is created or refreshed for **your** auctioneer account |
| Client **email** matches the bidder’s login email | The client is **linked** to that bidder |
| Client is marked as a **buyer** | They can be used in registration, clerking, and settlement like any other buyer client |

If they register for **another auctioneer’s** sale later, that company gets **its own** client record. Your list and settings stay separate from theirs.

---

## Two accounts, one person (example)

| Layer | Who sees it | What it is for |
|-------|-------------|----------------|
| **Bidder** | The person on the public site | Browse auctions, verify identity, register, bid online |
| **Your client** | You in **Customers** | Bid card number, tax, buyer’s premium, bid permission, notes for **your** business |

Think of the bidder account as the **person’s identity on Auction Journal**, and the client as **how your auction company works with that person**.

---

## Configuring settings for that bidder (on your client record)

Linking does **not** mean you control their bidder password or platform verification. It means you can set **your** rules on **their client profile** in the dashboard, for example:

- **Reserved bid card number** (or let the system assign one at registration)
- **Taxable** vs **tax exempt**
- **Buyer’s premium** (default or specific)
- **Bid permission** — approve, decline, limits, and notes that apply when they register for your auctions

Those settings live under **Customers** → open the client → **Buyer** on step 3 of create/edit. For **bid permission** (automatic approve/decline at registration), see [Set bid permission for a customer](bid-permission.md). For tax, bid card, and premium, see [Configure customer as buyer and seller](customer-preferences.md).

You can change these preferences anytime; they apply to **your** relationship with that buyer, not to other auctioneers.

---

## What updates automatically

| Updates | Direction |
|---------|-----------|
| Bidder changes **name** or **profile photo** on the public site | Can flow to **your** linked client (and other auctioneers’ linked clients for that bidder) |
| You edit the **client** in your dashboard | Stays on **your** client only—it does **not** change their bidder login or profile on the public site |

---

## Different from floor bidders and manual clients

- **Floor bidder** — See [Who is a floor bidder? How do I onboard one?](floor-bidder/index.md). On-site buyer; you enter their bids; not the same as an online bidder account.
- **Manual client** — You typed them in with no bidder link until their email matches a bidder or they register for your auction.
- **Seller (consigner)** — May be a client without ever being a bidder.

---

## Related questions

- [Who is a customer of an auctioneer? What customer types exist? How do I add a customer?](add-customer.md)  
- [In what ways are customers created for an auctioneer?](creation.md)  
- [How can an auctioneer configure each customer according to their preferences as a seller and as a buyer?](../sample_questions.md#customer)  
- [Who is a bidder? What does a bidder do in Auction Journal?](../bidder/role.md)
