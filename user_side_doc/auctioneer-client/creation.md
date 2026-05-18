[Auctioneer Client](./index.md) · [Auction Journal](../../index.md)

# In what ways are customers created for an auctioneer?

Your **customers** (called **clients** in the dashboard) can be added in several ways. All of them create a profile **under your auction company** for use in **your auctions**—registration, clerking, settlement, and mailing lists.

For **who** a customer is and the **manual add** steps, see [Who is a customer? How do I add one?](add-customer.md).

---

## Quick reference

| How | You do this | When the customer appears in your list |
|-----|-------------|----------------------------------------|
| **Add manually** | **Customers** → **Add Clients** | Right after you submit the 3-step form |
| **Import from file** | **Customers** → **Import Clients** | After the import finishes successfully |
| **Bidder registers for your auction** | Bidder uses the public site (no action from you) | When they complete registration for **your** auction |
| **Invite a seller** | **Customers** → **Onboard Seller** | After they complete the email registration link |
| **Onboard floor bidder** | **Customers** → **Onboard Floor Bidder** | Right after you submit that wizard |

---

## 1. Add a customer manually

You enter the person’s details yourself (or scan a driver’s licence to pre-fill).

1. Open **Customers** in the Auctioneer Dashboard.  
2. Select **Add Clients**.  
3. Choose **Add Client Data Manually** or the **licence scanner**.  
4. Complete the **3 steps** (contact, addresses, buyer/consigner settings) and **Submit**.

Full steps: [add-customer.md](add-customer.md).

---

## 2. Import customers from a spreadsheet

Use this when you already have many contacts outside Auction Journal.

1. Go to **Customers**.  
2. Select **Import Clients**.  
3. Download the **sample CSV** if you need the expected columns.  
4. Upload your file, **map** your columns to Auction Journal fields, and run the import.  
5. Review any **error report** if some rows fail.

Imported rows are added as clients on your account (typically set up as both buyer and consigner in the system). Fix errors in your file and import again if needed.

---

## 3. When a bidder registers for your auction

If someone has a **bidder account** on Auction Journal and **registers for one of your auctions**, Auction Journal **automatically** creates or updates a **client** record for them under **your** company.

- You do not need to add them manually first.  
- Their email on the bidder account is used to match the client.  
- They are linked as a **buyer** for your auctions.

This keeps your customer list aligned with people who actually sign up to bid at your sales.

---

## 4. Invite a seller to register themselves

For **sellers (consigners)** you want to onboard without typing everything yourself:

1. Open **Customers**.  
2. Select **Onboard Seller**.  
3. Enter the seller’s **email** (lowercase) and select **Invite**.  
4. They receive an email with a **registration link** (the link expires after a limited time—about **24 hours** in the product messaging).  
5. They open the link, fill in their details (similar to the manual client form), and submit.  
6. They then appear in your **Customers** list.

Until they complete that link, they are **not** yet a client in your list—the invite only sends the email.

Separate guides cover the seller’s view of the invitation and registration (see [sample questions](../sample_questions.md) — Customer section).

---

## 5. Onboard a floor bidder

For someone who bids **on the floor** at your auction (and is not using the normal bidder signup for that purpose):

1. Go to **Customers**.  
2. Select **Onboard Floor Bidder**.  
3. Complete the wizard and submit.

A floor bidder is treated as a **buyer** for your auctions. Do not use this flow if that person already has an Auction Journal **bidder** account with the same email—they should bid from their bidder account instead.

Details: [add-customer.md — Floor bidder](add-customer.md#floor-bidder-separate-path).

---

## Which method should I use?

| Situation | Suggested method |
|-----------|------------------|
| One new contact at the desk | **Add Clients** (manual or licence scan) |
| Many existing contacts in Excel/CSV | **Import Clients** |
| Seller should fill in their own details | **Onboard Seller** (invite) |
| On-site bidder without AJ login | **Onboard Floor Bidder** |
| They already bid online on your auction | Usually **automatic** when they register for the auction |

---

## Related

- [Auctioneer Dashboard — Customers](../auctioneeer/dashboard.md)  
- [Add a customer (manual)](add-customer.md)
