[Bidder](./index.md) · [Auction Journal](../index.md)

# Bidder registration (business logic)

End-to-end flow for **public bidder signup** on Auction Journal: partial account created at **SMS OTP**, then **one request** completes profile, credentials, and marks the bidder **registered**. Controllers: `mobileOTP`, `verifyOtp` (bidder branch), `registerBidder`. Public UI lives in **`auctionjournal-public`** under `src/app/Components/Auth/SignUp` (route example: `src/app/auth/signup/page.js`).

---

## Purpose

- Collect **identity and address**, then **verify phone** via OTP, then set **email/password** and consents.
- Reuse the same OTP HTTP routes as auctioneer signup; **request type** distinguishes bidder vs auctioneer in the backend.
- On success: bidder is **registered**, **newsletter** row is created, **two emails** (welcome-style + email verification), **bidder signup score** event, and the client shows a **check your email** success state.

---

## UI flow (two steps)

| Step | Screen (BIDDER SIGN UP) | What is collected |
|------|-------------------------|-------------------|
| **1** | Progress: **1** of **2** | First name, last name, street address (multiline), ZIP, city, state, country. ZIP can drive auto-fill; **country** is fixed **USA** in this client (field disabled). |
| **2** | Progress: **2** of **2** | Phone (+1, 10 digits after country code), **GET OTP** → OTP entry with **RESEND OTP** / **VERIFY OTP** → **Verified**; email + confirm email; password + confirm; terms checkbox; optional marketing lines; **Submit**. |

There is **no separate driving-license capture** on this public signup form; the backend still accepts optional `DrivingLicenseNo` / `DrivingLicensePhoto` (e.g. URLs) if ever sent.

---

## Backend: `mobileOTP` (bidder, `reqType` bidder)

- Same route as auctioneer: `POST /api/user/registration/otp/send` with **`reqType` = bidder** (numeric `2` in the public client).
- If phone is already tied to a **fully registered** bidder → **reject** (“phone number already registered”).
- Sends SMS (Twilio), sets **OTP** and **short expiry** (minutes-scale).
- If a bidder row already exists for that phone but is not registered → **update** OTP fields.
- If none exists → **create** bidder with phone, OTP, expiry, generated **BidderID**, and a **placeholder email** (`temp-{BidderID}`) until full registration replaces it.

---

## Backend: `verifyOtp` (bidder)

- Route: `POST /api/user/registration/otp/verify` with **`reqType` = bidder**.
- On **matching OTP** and **not expired** → set **`is_PhoneVerified`** for that bidder; response includes **`id`** (Mongo document id) used later as `req.body.id` for `registerBidder`.
- Wrong OTP or expired OTP → **400** with explicit messages.

---

## Backend: `registerBidder`

- Route: `POST /api/bidder/register` (with `validateBidderRegister`).
- **Email uniqueness:** any existing bidder with the same email → **reject**.
- Load bidder by **`req.body.id`** (from verify step). If already **`isRegistered`** → **reject** (“Action Prevented”).
- **`is_PhoneVerified`** must be true → else **reject** (“please verify mobile number”).
- **Updates** that bidder with: new **BidderID** (regenerated on completion), name, address, phone, ZIP/city/state/country, email, hashed password, optional driving license fields, **subscribedNewsletter**, email verification secret + long expiry, **`isRegistered: true`**.
- Side effects: **newsletter** subscribe for email, **two** `sendEmailToCustomer` calls (signup content + verification), **`createBidderScore`** for signup reason, success message instructing user to **verify email**.

---

## Validation highlights (server)

`validateBidderRegister` enforces presence/format for: names, **streetAddress**, city, state, country, ZIP (max length 5), **Phone** (length band consistent with **+1** U.S. style), **Email**, **password** strength (min 8 + complexity), **`subscribedNewsletter`** present (validator text ties to newsletter checkbox). Driving license fields are optional with URL rule when provided.

---

## Public client behavior (contract notes)

- **OTP UI:** After GET OTP, the same row shows OTP field with **Resend** and **Verify**; after success, phone is disabled and **Verified** is shown (matches provided screenshots).
- **Submit payload:** Client posts the accumulated `data` object (includes `id` from verify, `Phone`, `Email`, address from step 1, passwords, flags). **`subscribedNewsletter`** must satisfy the server validator for registration to succeed even though step-2 **Yup** schema in code does not list every server requirement (e.g. phone-verified flag is enforced on the server, not only in Yup).
- **OTP send payload** includes a misspelled optional license field key in the HTTP client; the bidder **mobileOTP** path does not depend on it.

---

## HTTP surface (reference)

| Step | Method and path |
|------|-----------------|
| Send SMS OTP | `POST /api/user/registration/otp/send` |
| Verify SMS OTP | `POST /api/user/registration/otp/verify` |
| Complete bidder signup | `POST /api/bidder/register` |

---

## Errors (business wording)

- Phone already registered (OTP send).
- Incorrect OTP / expired OTP (verify).
- Email already registered; phone not verified; action prevented (already registered); generic server errors on register.

---

## After registration (user outcome)

- Success popup / redirect copy: **check email** to verify the address.
- Platform **verification** (identity + card) is a **later** stage — see [Verification](./verification.md); registration only establishes the account and email verification pending state.
