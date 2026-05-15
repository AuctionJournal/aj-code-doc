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

## Screenshots (application UI)

User guides should show **what the reader sees in the product**, especially **forms** (fields, labels, buttons, step order).

- **Store files only under `user_side_doc/image/<module>/`** (e.g. `user_side_doc/image/bidder/verification-step1-payment.png`). Do not put user-facing screenshots in `aj-code-doc/images/` or other dev-only folders.
- **Embed in markdown** so images render inline in the doc viewer:

  ```markdown
  ![Short description of the screen](../image/bidder/verification-step1-payment.png "Optional hover title")
  ```

  Use a **relative path from the `.md` file** to `user_side_doc/image/<module>/` (e.g. from `user_side_doc/bidder/page.md` → `../image/bidder/...`). Do **not** use `/image/...` alone — that breaks preview in VS Code/Cursor and many static viewers.

- **Place each image** immediately after the step it illustrates.
- **Add a one-line caption** under each image in italics if the alt text alone is not enough.
- **Prefer form screenshots** for multi-step flows: empty state, filled form, submit button, and post-success screen when relevant.
- **Capture from the real app** when possible; keep PII out of shots (test data, masked cards).
- **Developer docs** (`aj-code-doc/` outside `user_side_doc/`) should **not** embed UI screenshots; link to the matching user page instead (see [`../ai_generation_guide.md`](../ai_generation_guide.md)).

---

## Dev doc changes and keeping user docs in sync

Developer docs live in [`aj-code-doc`](../). User guides live here in **`user_side_doc`**. They are **not** updated by a background job: during an **AI-assisted or manual edit** to dev docs, whoever updates should keep them aligned when the change affects what users see or do.

**Rules (especially for AI):**

1. After changing **`aj-code-doc`** in a way that **should** change user-facing steps or wording, **ask the developer** first: *“Should I update the matching content in `user_side_doc` now?”*
2. **Do not** edit `user_side_doc` without that confirmation when the change originated from dev-doc work.
3. If the developer says **no**, the user-side update **cannot be done**, or they **stop** the work — add the **standard pending marker** to the **dev-side** file (see [`../ai_generation_guide.md`](../ai_generation_guide.md) section *User-side documentation sync*) so nothing is forgotten. Remove the marker once `user_side_doc` is updated.

This way “automatic” means **procedural discipline in each session**, not silent overwrites.

---

## Updating this guide

When the team agrees on a new convention (“always do X,” “never do Y,” “see `folder` for Z”), **add it here** so the same instructions do not need to be repeated in chat. This file is the **accumulated checklist** for user-side doc work.

**Cursor:** The repo rule `.cursor/rules/aj-code-doc-generation.mdc` (`alwaysApply: true`) tells the AI to read this file and `aj-code-doc/ai_generation_guide.md` before editing under `aj-code-doc/`.
