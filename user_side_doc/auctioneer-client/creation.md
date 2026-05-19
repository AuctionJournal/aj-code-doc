[Auctioneer Client](./index.md) · [Auction Journal](../../index.md)

# In what ways are customers created for an auctioneer?

Your **customers** (called **clients** in the dashboard) can be added in several ways. All of them create a profile **under your auction company** for use in **your auctions**—registration, clerking, settlement, and mailing lists.

For customer types and step-by-step manual entry, see [Who is a customer of an auctioneer? What customer types exist? How do I add a customer?](add-customer.md).

---

## Quick reference

| How | You do this | When the customer appears in your list |
|-----|-------------|----------------------------------------|
| **Add manually** | **Customers** → **Add Clients** | Right after you submit the 3-step form |
| **Import from file** | **Customers** → **Import Clients** | After the import finishes successfully |
| **Bidder registers for your auction** | Bidder uses the public site (no action from you) | When they complete registration for **your** auction — see [bidder vs customer](bidder-relationship.md) |
| **Invite a seller** | **Customers** → **Onboard Seller** | After they complete the email registration link |
| **Onboard floor bidder** | **Customers** → **Onboard Floor Bidder** | Right after you submit that wizard |

---

## 1. Add a customer manually

You enter the person’s details yourself (or scan a driver’s licence to pre-fill).

1. Open **Customers** in the Auctioneer Dashboard.  
2. Select **Add Clients**.  
3. Choose **Add Client Data Manually** or the **licence scanner**.  
4. Complete the **3 steps** (contact, addresses, buyer/consigner settings) and **Submit**.

---

## 2. Import customers from a spreadsheet

Use this when you already have many contacts outside Auction Journal.

See the full walkthrough: [How do I import many existing customers?](import-clients.md).

1. Go to **Customers** → **Import Clients**.  
2. Download **CSV format** or **Export Clients** for a reference file.  
3. Upload your CSV, **map** columns, **preview**, then run the import (optional **overwrite**).  
4. Review the results summary and **Download Report** if any rows fail.

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

- **You (auctioneer):** [How can I invite a seller to self-register?](invite-seller.md)  
- **The seller (email link):** [How does a seller self-register from the invitation?](seller-self-register.md)

Until they complete the registration link, they are **not** yet a client in your list—the invite only sends the email.

---

## 5. Onboard a floor bidder

For someone who bids **on the floor** at your auction (and is not using the normal bidder signup for that purpose):

1. Go to **Customers**.  
2. Select **Onboard Floor Bidder**.  
3. Complete the wizard and submit.

A floor bidder is treated as a **buyer** for your auctions. Do not use this flow if that person already has an Auction Journal **bidder** account with the same email—they should bid from their bidder account instead.

For more on floor bidders, see [Who is a floor bidder? How do I onboard one?](floor-bidder/index.md).

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

## Related questions

- [Who is a customer of an auctioneer? What customer types exist? How do I add a customer?](add-customer.md)  
- [How can an auctioneer invite a seller to self-register as their customer?](../sample_questions.md#customer)  
- [Who is a floor bidder? How do I onboard one?](../sample_questions.md#customer)  
- [Auctioneer Dashboard — Customers menu](../auctioneeer/dashboard.md)
