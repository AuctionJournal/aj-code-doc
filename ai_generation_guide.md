Generation of doc

This doc will be used by developers, project manager, client. This doc will contain business logic and implementation logic, which should be easy to read and understand by a person without much coding knowledge.

This doc will be manually updated by developer or will be updated by developer with the help of AI. When AI is updating the doc it should not wipe the existing content in doc, instead it should update from the given code base reference and the doc itself. While updating doc it should be done in interview mode, thus AI will act as technical writer by observing the code base reference and asking the developer about each thing then update the doc.

## Doc format

Doc will be in module wise.

## Code base references

- **AJ-Main-Backend** — The main backend of whole AJ
- **auctioneer_dashboard_revamp** — The auctioneer CRM Dashboard
- **auctionjournal-public** — Public marketing and bidder dashboard
- _(add others as needed)_

## User-side documentation sync (`user_side_doc`)

When **developer-side** docs under `aj-code-doc` change in a way that **should** be reflected in end-user guides under [`user_side_doc`](user_side_doc/):

1. **Identify** the related page(s) in `user_side_doc` (same topic / module mirror).
2. **Ask the developer** explicitly: whether to update the corresponding user-side doc now (do not silently rewrite user docs).
3. If they **approve** — update `user_side_doc` so behavior and steps stay aligned with the dev doc / code.
4. If they **decline**, the update **fails**, or work is **stopped** on purpose — record that in the **dev-side** doc that was changed, using the standard marker below. Remove the marker after user-side doc is updated.

**Standard marker** (paste at the top of the affected dev doc section, or just under the file title):

```markdown
> **User-side doc:** Pending — sync with `user_side_doc/…` after dev doc change (_reason optional_).
```

Replace `user_side_doc/…` with the path(s) to update (e.g. `user_side_doc/auctioneeer/registration.md`).

For AI sessions: also read [`user_side_doc/ai_generation_guide.md`](user_side_doc/ai_generation_guide.md) for tone, structure, and naming rules when editing user docs.

## Cursor (AI in this repo)

The project includes **`.cursor/rules/aj-code-doc-generation.mdc`** (`alwaysApply: true`) so new chats are instructed to read this file and `user_side_doc/ai_generation_guide.md` before changing anything under `aj-code-doc/`. You can disable or edit that rule in Cursor if you need a session without it.
