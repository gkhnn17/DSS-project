# Project diary

One entry per week, written at the end of the week in its own commit. Earlier entries are never edited.
Each entry records what went wrong and what stayed unclear, not what was done (the repository history shows that).

## Generative AI use (declared)

- Claude Sonnet 5.5 (Claude Code): read the course notes and the transcript, explained the method and the open points, planned the work split, wrote this diary and `PHASE1_PLAN.md`, coordinated the other agent.
- Claude Opus (Claude Code, subagent): drafted the domain model (`domain*.puml`), the use case model (scenarios, `ucd_*.puml`, specifications) and the decision logs (`domain_decisions.md`, `requirements_decisions.md`).
- Phase 2 (design): Claude (Claude Code) drafted `2_Design/` (decisions D56-D74, exclusions, interfaces, architecture, extension points, sequence diagrams and the purchase state machine) from the scope restriction, the Phase 1 models and the group's answers on voids (customers can remove items), card credit (extension point only), the purchase count and the payment split.
- The conversations are kept in the Claude Code session history and can be shown at the discussion.
- The group reviews and takes responsibility for everything submitted; drafts are not final until each member can explain their part.

## Group work (members)

Gökhan Dumlupınar, Rufino Moreno and Alonso worked on every part of Phase 1 together; no part belongs to one member alone.

- Transcript: all three read it and discussed the open points until we agreed on each one.
- Domain model: all three checked every view (`domain_stores`, `domain_purchases`, `domain_validation`, `domain_campaigns`, `domain_payment`) and the full `domain.puml` against the transcript, and corrected what did not fit.
- Use case model: all three went through the scenarios, the specifications and the use case diagrams, and changed what was unclear or unsupported.
- Decision logs: all three confirmed or changed every decision in `domain_decisions.md` and `requirements_decisions.md`, including the gaps we found when we compared the two models (D51-D55).
- Repository and diary: all three take part in keeping the repository, the diary and the declaration of AI use up to date.
- Discussion: each member can explain every part of the submission.
- The first drafts were made with the AI tools declared above; the group reviews and takes responsibility for the result.
- The phases, deadlines and timeline are in `PROJECT_HISTORY.md`.

## Week of 26 October 2026 (entry dated 2026-10-28)

- The transcript leaves many points open or contradictory (selection of purchases for validation, overlapping campaigns, payment order, family cards, cancellation with balance and vouchers, scales). We decided each one in the decision log, but several decisions are only our reading and depend on the forum answers, which we have not seen yet.
- The first domain model was drafted without the use cases and lacked things the use cases then needed (current product price, products sold by weight, the void threshold, campaign values, the clean-history parameter). We only found this after writing the use case model and had to add D51-D55. Lesson: check the two models against each other earlier.
- The agent that drafted the models widened some of our decisions (for example "no stacking" became "at most one campaign application per line, card credit included", D11) and added unrequested choices (D34, D36, D41, D43, D44). We have not yet checked each of them against the transcript.
- Still unclear: whether percentage or fixed amount is meant for the discounts and card credit (D54), who changes a station's mode and who defines campaigns (the transcript does not say), and whether the Phase 1 deadline of 23 October has any consequence for this version.

## Week of 16 November 2026 (entry dated 2026-11-20)

- We discussed the open points of the scope restriction as a group and decided each one in `2_Design/design_decisions.md` (D56-D74): customers can remove an item before payment (D63), the verification count is paid purchases (D60), the customer chooses the balance amount and the bank card pays the rest (D68), and card-credit campaigns are only an extension point (D67). We planned the design work (architecture, interfaces, sequence diagrams, state machine, exclusions) and split the review between us.
- The forum still has no teacher answer, so all of these are our own readings of the scope restriction. D63, D60 and D68 change Phase 1 decisions D20, D5 and D16.
- What went wrong: we did not write diary entries for the weeks of 2 and 9 November at the time, so there is a gap since 28 October.
- We have not seen any teacher feedback on the Phase 1 submission (we could not find it in the repository or the forum), so we continued with the design on our own reading of the scope restriction. If feedback turns up, we will check the models against it.
