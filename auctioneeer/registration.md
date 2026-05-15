[Auctioneer](./index.md) · [Auction Journal](../index.md)

# Auctioneer registration (business logic)

This document describes how an auctioneer account is created end-to-end: what the user does in the dashboard signup flow, what the backend enforces, and what happens after a successful submit. It mirrors the **auctioneer dashboard revamp** signup (`SignUp` steps 1–3) and the auth controllers `mobileOTP`, `verifyOtp`, and `registerAuctioneer`.

---

## Purpose

- Create an **Auctioneer** record tied to a **verified mobile number**, then complete **profile, company, and credentials** so the user can be marked **registered** and enter the **email verification** lifecycle.
- Prevent duplicate identities (phone already fully registered, email already used) and prevent **double completion** of signup for the same partial account.

---

## End-to-end flow (three UI steps)

The UI presents a **three-step wizard**. Data from earlier steps is held in client state until the final submit. The **server is authoritative** for phone OTP and for the final persistence rules.

| UI step | What the user provides (business meaning) |
|--------|---------------------------------------------|
| **Step 1** | Individual identity and mailing address: first and last name, street address, ZIP, city, state, country. ZIP can drive auto-filled location fields where the client supports it. |
| **Step 2** | Business identity: company name, **company logo** (uploaded and stored as a URL before register), **website**, **auctioneer license number**, optional **license document** image (uploaded URL if provided). |
| **Step 3** | **Mobile verification** (SMS OTP), **email** (with confirmation), **password** (with confirmation), **terms acceptance**, and **newsletter** consent. Submitting this step calls **full registration** on the server. |

**Important:** Step order in this product is **profile and address → company and licensing → phone/email/password and submit**. Any screenshots that order steps differently refer to a different layout or an older concept; this doc follows the revamp codebase.

---

## Step 1 — Profile and address

**Intent:** Capture the person and location that will be stored on the auctioneer account.

- **Required (client):** First name, last name, street address, ZIP, city, state, country.
- **Defaulting:** Country may default (e.g. USA) in the UI.
- **ZIP assist:** When ZIP changes, the client may resolve city/state/country automatically for convenience; the user can still correct values.

No server call is required to move from step 1 to step 2; this is **client-side validation** only until the final registration request.

---

## Step 2 — Company and license

**Intent:** Tie the account to a **business** and evidence of licensing; establish branding (logo).

- **Required (client):** Company name, company logo (image), website (format validated in UI), auctioneer license number.
- **Optional:** Scan/image of the license; if present, it must be an image type. Large images may be compressed in the browser before upload.
- **Uploads:** Logo (and optional license file) are uploaded to blob storage; **registration sends URLs** to the backend, not raw files.

Moving to step 3 is again **client-validated**; the server does not yet receive this bundle as a final account.

---

## Step 3 — Phone OTP, credentials, consent, submit

**Intent:** Prove control of the phone number, set login email/password, capture legal/marketing consent, then **atomically** complete registration for the auctioneer stub created at OTP time.

### Phone verification (SMS)

1. User enters a **US-format** mobile number with country code **+1** (UI enforces length/pattern).
2. **Send OTP** calls the server with `reqType` meaning **auctioneer** (same path as bidder signup, distinguished by type).
3. **Server behavior (auctioneer path):**
   - If the phone is already tied to an auctioneer who is **fully registered**, the request is **rejected** (phone already registered).
   - Otherwise an OTP is generated, an **expiry time** is set (short window, on the order of **minutes**), and the code is delivered via **SMS** (Twilio).
   - If an auctioneer row already exists for that phone but is **not** registered yet, its OTP fields are **updated**.
   - If no row exists, a **new** auctioneer document is created with phone, OTP, expiry, and a generated **Auctioneer ID** (business identifier prefix `AU…`).
4. User enters the OTP and **Verify**:
   - If OTP matches and expiry has not passed, the server sets **phone verified** on that auctioneer and returns identifiers the client needs for the final submit (including document `id` / auctioneer id as applicable).
   - Wrong OTP or expired OTP returns **clear business errors** (incorrect vs expired).
5. **Resend OTP** repeats the send path and refreshes OTP and expiry on the same partial record.

Until phone verification succeeds, **final registration must not proceed** (the server also enforces this).

### Email and password

- **Email** must be valid format and **lowercase** (UI rule); must match **confirm email**.
- **Password** must meet complexity rules (length, upper, lower, number, special character); must match **confirm password**.

### Consent and marketing

- **Terms and conditions** must be accepted (required).
- **Newsletter** checkbox is **required to be checked** in the current revamp validation before submit (the field is treated as mandatory consent to subscribe for signup to succeed).
- A separate “featured auctions” style line may appear in the UI; if it is not wired to the payload, it has **no backend effect** unless added later.

### Final submit (`registerAuctioneer`)

On submit, the client sends **one payload** that merges: step 1 and 2 fields, blob **URLs** for photo/license, verified **phone**, **email**, **password**, **newsletter** flag, and the **MongoDB id** of the auctioneer row created/updated during OTP (so the server updates the **same** partial account).

**Server guards (business order):**

1. **Email uniqueness:** If any auctioneer already exists with that email, registration is **rejected** (email already registered).
2. **Load auctioneer by id** from the request: must exist and correspond to the OTP flow.
3. **Already registered:** If this account is already marked registered, the request is treated as **blocked** (anti-abuse / no double signup).
4. **Phone verified:** If phone is not verified on that record, registration is **rejected**.
5. **Persist:** Company, personal, address, email, photo URL, license number and optional license URL, website, hashed password, generated **email verification** secret and expiry (long window, on the order of **hours**), **ticker symbol** derived from company and city, newsletter preference, and **`isRegistered = true`**.

**On success (business outcomes):**

- Welcome / verification **email** is sent to the auctioneer (separate template flow from SMS).
- **Default miscellaneous accounts** are provisioned for downstream operational setup.
- Email is added to the **newsletter** list when the flow subscribes the user (aligned with the newsletter field on registration).

The user sees a **success** state in the UI; they still need to complete **email verification** via the link in mail before the account is fully trusted for all flows (see main [Auctioneer](./index.md) lifecycle).

---

## HTTP surface (reference)

| Concern | Method and path (under API base) |
|--------|-----------------------------------|
| Send SMS OTP | `POST /api/user/registration/otp/send` |
| Verify SMS OTP | `POST /api/user/registration/otp/verify` |
| Complete auctioneer signup | `POST /api/registerAuctioneer` |

Request bodies are trimmed server-side. Auctioneer vs bidder OTP flows share these routes and are distinguished by **`reqType`** (auctioneer uses the auctioneer value).

---

## Validation highlights (server vs client)

- **Phone (send):** Server validates presence and **length** compatible with **+1** and national digits (validator message reflects “12 including country code +1”).
- **OTP verify:** Server expects phone and OTP present; business rules are **match + not expired**.
- **Register:** Server validates core fields including **email format**, **password strength**, **ZIP max length**, **photo as URL**, **newsletter field present**, optional **URLs** for website and license image. Some body fields used by the controller (e.g. street address) may be **stricter in the UI** than in every server check; the UI still collects them for persistence when sent.

---

## Error and edge cases (what the user or support might see)

- **Phone already registered** — Send OTP when that number already completed auctioneer registration.
- **Incorrect OTP** / **OTP expired** — Verify step; user should resend or re-enter within the window.
- **Email already registered** — Final submit; choose another email.
- **Please verify mobile number** — Final submit without verified phone on the stub record.
- **Action prevented** — Final submit attempted for an id that is already registered (should be rare if the client state is correct).
- **Generic failure** — SMS or DB/email side errors; user retries later.

---

## Summary

Auctioneer registration is a **staged story**: collect person and company data in the UI, use **SMS OTP** to create or refresh a **partial auctioneer** and mark the phone verified, then **one final POST** promotes that record to a **registered** auctioneer with credentials, URLs for assets, ticker symbol, and triggers **email verification** and **default account** setup. Duplicate **email** or already-registered **phone** are blocked at the appropriate step.
