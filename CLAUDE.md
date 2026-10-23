# CLAUDE.md

Guidance for Claude sessions working in this repository.

## Project
Hotel Go Big: an all-in-one platform to run a hotel or a network of hotels. Currently in the
**planning stage** — the repository holds product documents, not code yet.

## Read first, every session
1. [`docs/decision-log.md`](docs/decision-log.md) — what has been decided, why, and what's still open.
2. [`docs/PRD.md`](docs/PRD.md) — vision, segments, roadmap, data model.
3. [`docs/phase-0-spec.md`](docs/phase-0-spec.md) and [`docs/phase-1-spec.md`](docs/phase-1-spec.md) — detailed requirements.
4. [`docs/partner-questions.md`](docs/partner-questions.md) — questions for PayHere, Channex and others.
5. [`docs/advisor-questions.md`](docs/advisor-questions.md) — questions for advisors (e.g. Lahiru).

## Working with the founder
- The founder is not a software engineer: explain technical terms in plain language, with examples.
- When the founder asks to be **reminded of partner questions**, give each question **together with
  its full "why we're asking" explanation** and the "if the answer is no" consequence from
  `docs/partner-questions.md`. Longer is fine; never give the bare questions only.
- When the founder makes or changes a decision, **update `docs/decision-log.md`** (date, decision,
  status, why) and any affected spec, in the same change.
- When partner answers come in, fill in the **Answer** fields and update the decision log.
- **Advisor questions (e.g. Lahiru):** when the founder asks to be reminded, give each question with
  its "why we're asking" from `docs/advisor-questions.md`. While working, whenever something comes up
  that an advisor could help with (Lahiru: built and sold an HR/payroll app with Claude Code, knows
  security practices like VAPT), **suggest it as a candidate question**, but add it to the file
  **only after the founder confirms**.
- Launch market is **Sri Lanka**; check local facts (tax, payments, regulation) rather than assuming
  rules from other countries, and say when information couldn't be verified.

## Merging
- **Never merge to `main` without the founder's explicit go for that specific merge.** No auto-merging.
- Whenever changes are pushed and waiting to be merged, **end the response with a clear reminder**
  that a merge is pending and what it contains.

## Conventions
- Docs live in `docs/`, written in Markdown.
- Requirement IDs: `P0-<AREA>-NN` for Phase 0, `FR-<AREA>-NN` for Phase 1.
