# User-side documentation — guide for authors and AI

This folder documents **Auction Journal for people who use the product** (auctioneers, bidders, and similar), not for engineers implementing it.

Developer-oriented detail lives under [`aj-code-doc`](../) (controllers, APIs, internal rules). **Use that codebase and dev docs as the source of truth** when writing or updating user docs so steps and rules stay accurate. User docs should explain **what to do and why it matters**, not stack traces, route handlers, or internal field names unless the reader truly needs them.

---

## Format and structure

- Prefer **question-and-answer** or **clear step-by-step** prose that matches how support or onboarding would speak.
- **Mirror the module layout** of `aj-code-doc`: same topic areas and folder names (e.g. `auctioneeer`, `listing`, `auction`) so topics stay findable in parallel between dev and user doc trees.
- When a numbered question in [`sample_questions.md`](sample_questions.md) gets a full answer, add a **dedicated page** under the right module and **link the question** to that page instead of duplicating long answers only in the questions file.

---

## Voice and tone

- Write **to the reader** (e.g. “Enter your company name”) — you are helping an auctioneer or bidder complete a task.
- **Do not** label the material as “user-facing help,” “end-user documentation,” or similar meta framing in the body or module intros. The reader already knows they are a user; keep breadcrumbs minimal (e.g. module home · Auction Journal).
- Avoid unnecessary internal jargon (“payload,” “controller,” “reqType”). Prefer UI labels and outcomes (“Select **Submit**,” “You will receive a text message”).

---

## File and page naming

- Put guides **inside the module folder** they belong to (e.g. auctioneer registration lives under `user_side_doc/auctioneeer/`).
- Use **short, contextual filenames** when the folder already implies scope: e.g. `registration.md` under `auctioneeer/`, not `register-as-auctioneer.md`.
- Use a **specific title** at the top of the page that matches the user’s question or task (the H1 can be the question itself).

---

## Breadcrumbs and cross-links

- Keep the top-of-page trail short: typically **module index** and **Auction Journal** home — avoid extra tiers like “User documentation” unless the site map truly requires it.
- Link related flows (e.g. Help and Support) where “if you’re stuck” fits naturally.

---

## Updating this guide

When the team agrees on a new convention (“always do X,” “never do Y,” “see `folder` for Z”), **add it here** so the same instructions do not need to be repeated in chat. This file is the **accumulated checklist** for user-side doc work.
