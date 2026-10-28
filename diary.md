# Project diary

One entry per week, written at the end of the week in its own commit. Earlier entries are never edited.
Each entry records what went wrong and what stayed unclear, not what was done (the repository history shows that).

## Generative AI use (declared)

- Claude Sonnet 5.5 (Claude Code): read the course notes and the transcript, explained the method and the open points, planned the work split, wrote this diary and `PHASE1_PLAN.md`, coordinated the other agent.
- Claude Opus (Claude Code, subagent): drafted the domain model (`domain*.puml`), the use case model (scenarios, `ucd_*.puml`, specifications) and the decision logs (`domain_decisions.md`, `requirements_decisions.md`).
- The conversations are kept in the Claude Code session history and can be shown at the discussion.
- The group reviews and takes responsibility for everything submitted; drafts are not final until each member can explain their part.

## Week of 26 October 2026 (entry dated 2026-10-28)

- The transcript leaves many points open or contradictory (selection of purchases for validation, overlapping campaigns, payment order, family cards, cancellation with balance and vouchers, scales). We decided each one in the decision log, but several decisions are only our reading and depend on the forum answers, which we have not seen yet.
- The first domain model was drafted without the use cases and lacked things the use cases then needed (current product price, products sold by weight, the void threshold, campaign values, the clean-history parameter). We only found this after writing the use case model and had to add D51-D55. Lesson: check the two models against each other earlier.
- The agent that drafted the models widened some of our decisions (for example "no stacking" became "at most one campaign application per line, card credit included", D11) and added unrequested choices (D34, D36, D41, D43, D44). We have not yet checked each of them against the transcript.
- Still unclear: whether percentage or fixed amount is meant for the discounts and card credit (D54), who changes a station's mode and who defines campaigns (the transcript does not say), and whether the Phase 1 deadline of 23 October has any consequence for this version.
