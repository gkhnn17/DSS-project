# Phase 1 plan: requirements analysis

Scope: only the Phase 1 deliverables (domain model, use case model, decision log), plus the project diary.
Specification: the transcript in Appendix A of the assignment. Nothing outside it, or a recorded decision, goes into the models.

## Work split

| | **Member 1: Purchase modes** | **Member 2: Validation and control** | **Member 3: Pricing, campaigns, payment** |
|---|---|---|---|
| Name | _(fill in)_ | _(fill in)_ | _(fill in)_ |
| Domain model views | `dominio_lojas`, `dominio_compras` | `dominio_validacao` | `dominio_campanhas`, `dominio_pagamento` |
| Use cases | Assisted checkout, self-checkout, scan-while-shopping, weighed items, changing a station's mode | Validation, configuring the validation rule, voids and supervisor authorisation, monthly report | Defining and applying campaigns, coupons, payment with several instruments, cancelling a purchase |
| Decision log | D3, D9, D21, D22, D25-D27, D30 | D4-D7, D20, D29 | D8, D10-D19, D23, D24, D28 |

If the group has more than three members, the extra members take the shared tasks below.

## What each member does for Phase 1

1. Check their domain views against the transcript, line by line. Remove anything without support in the transcript or a recorded decision; add what is missing.
2. Check their use case specifications: every step is supported by the transcript or a decision, and every entity used exists in the domain model.
3. Read their decision log entries, confirm or change them, and be able to justify each in their own words.
4. Find the forum answers (if any) for their area and update the affected decisions.

## Shared tasks

| Task | Owner |
|---|---|
| Consistency of the full `dominio.puml` and the general use case diagram | Member 1 |
| Merging the decision logs into one file and renumbering conflicts | Member 3 |
| Declaring generative AI use in the diary (what for, which tool) | Member 2 |
| Weekly `diary.md` entry (3-4 lines: what went wrong, what remains unclear), one entry per week, own commit, on its date | rotating |

## Rules to remember

- Anything in the models can be asked of any member in the discussion; a part nobody can explain counts as not done. Once a week, each member explains their part to the others (10 minutes).
- Do not rewrite earlier diary entries. Each entry is written in its own commit on the date it refers to.
- Keep the conversations with AI tools; they may be requested at the discussion.
- UML is kept as `.puml` source files in the repository, not only as images.
