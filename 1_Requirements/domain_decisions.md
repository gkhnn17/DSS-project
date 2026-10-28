# Decision log: Domain Model (Phase 1)

Source of truth: transcript in Appendix A of the assignment. Model files: `domain.puml` (complete model)
and the views `domain_stores.puml`, `domain_purchases.puml`, `domain_validation.puml`,
`domain_campaigns.puml`, `domain_payment.puml` (subsets of the same model).
Tags: **[confirmed by group]** = decided by the group; **[confirm with forum]** = default that depends on a
client answer on the course's client questions forum (question numbers Qn refer to the 2026-10-21 snapshot).

## Model structure

**D1 – One model, several diagrams**
Open question: can the domain model be split into sub-models (forum Q1)?
Decision: one model in `domain.puml`; five view diagrams repeat only the classes of each area.
Justification: "diagrams are not the models" (T03 s26): views improve readability without creating separate models.
Depends: all `.puml` files. [confirm with forum]

**D2 – Derived values are not modelled**
Open question: should derived values be explicit, e.g. the purchase total (forum Q1)?
Decision: totals, the card balance, the customer's validation history and the monthly void report are left out because they can be derived.
Justification: they follow from Item Registration, Campaign Application, Payment, Card Credit, Validation and Void; storing them twice risks inconsistency.
Depends: Purchase, Customer Card, Validation, Void. [confirm with forum]

**D3 – The three modes are kinds of Purchase**
Open question: how to represent assisted checkout, self-checkout and scan-while-shopping?
Decision: Assisted Purchase, Self-Checkout Purchase and Scan-While-Shopping Purchase are linked to Purchase by "is a". Lines, campaigns, payments and voids hang off Purchase.
Justification: the assignment says the modes "share concepts, pricing rules, payment instruments and sales records". Only the differences (operator, validation, station mode) are attached to the subtypes.
Depends: Purchase and its three "is a", note on Purchase.

## Validation (scan-while-shopping only)

**D4 – Unexplained discrepancy: new assisted purchase, old one kept as evidence**
Open question: "the purchase goes back ... scanned again at an assisted checkout"; registered items "stay ... as evidence" but "can't keep counting for payment".
Decision: Unexplained Discrepancy "sends purchase to" a new Assisted Purchase. The original purchase keeps its lines and moves to state "sent to assisted checkout", and it is never paid.
Justification: the evidence stays untouched and linked. Payment applies only to the new purchase, so the old lines stop counting without being deleted.
Depends: Unexplained Discrepancy–Assisted Purchase, Purchase State, note on Item Registration. [confirmed by group]

**D5 – Customer history = validations without discrepancy**
Open question: what counts in the customer's history (forum Q10)?
Decision: history is the set of past Validations of the card's purchases. Only outcomes "No Discrepancy" count as clean and lower the chance of selection.
Justification: Hélder: "checked five times without a problem should be bothered less". History is derived (D2), so no extra entity is needed.
Depends: Validation, Validation Outcome, Purchase–Customer Card. [confirmed by group]

**D6 – Selection rule is configured by each store**
Open question: Hélder (history, forced checks) and Sandra ("rare") disagree, and "it's not going to be settled by you" (forum Q2, Q3.1).
Decision: a Validation Policy per store with a base random percentage, reduced by clean history and forced above an amount or when a flagged product is registered. The model fixes no numeric values.
Justification: this covers both positions and leaves the trade-off to the business, as the transcript asks.
Depends: Validation Policy (basePercentage, amountThreshold), Store–Validation Policy, Validation Policy–Product "flags as problematic". [confirmed by group]

**D7 – Small vs unexplained difference decided by the staff member**
Open question: how is a "small difference" distinguished (forum Q2, Q10)?
Decision: there is no numeric threshold. The validating staff member classifies the outcome, and a good-faith explainable difference counts as small. Outcome is an entity with three kinds.
Justification: the transcript defines "small" by explanation ("usually in good faith"), not by value.
Depends: Validation Outcome and its three "is a", Validation–Staff Member. [confirmed by group]

**D29 – Price of an item added during validation**
Open question: what price applies to an item the staff member adds after a small discrepancy?
Decision: it is a normal Item Registration with registeredAt = the moment it is added, so it takes the price and campaigns in force then.
Justification: this applies the "price at registration" rule (D8) without exceptions. The item had never been registered before.
Depends: Small Discrepancy "adds" Item Registration. [confirm with forum]

## Prices and weighed items

**D8 – Price is fixed at registration time**
Open question: is the price taken at registration or at payment? (The transcript is explicit: registration.)
Decision: Item Registration stores registeredAt and unitPrice. Campaigns are evaluated against registeredAt (Campaign start/end).
Justification: the August complaint ("he was right"). With scan-while-shopping, registration and payment can be an hour apart.
Depends: Item Registration attributes, Campaign start/end, notes.

**D9 – Weighed items: the price from weighing time**
Open question: what price does a labelled weighed item take?
Decision: the line is "priced by" a Weighing whose price was computed at weighedAt. Campaign evaluation for that line uses the weighing time too.
Justification: Alberto: "it goes with the price at that time". This is the only exception to D8, and it is recorded explicitly.
Depends: Weighing (weighedAt, price), Item Registration–Weighing, note. [confirm with forum]

**D22 – Section scale and checkout scale are separate entities**
Open question: one or two scale entities (forum Q4.1, Q13)?
Decision: Section Scale (prints the Label) and Checkout Scale (weighs at the station) are separate entities. Each Weighing is done by exactly one of them. Assisted Checkout has one Checkout Scale, and a Self-Service Station has 0..1.
Justification: they play different roles (label vs direct weighing). The 0..1 for self-service follows the reading of "at self-checkout it works too", which is still open.
Depends: Section Scale, Checkout Scale, Weighing, Label. [confirmed by group]. The 0..1 on Self-Service Station is still [confirm with forum].

**D30 – Weighed items in scan-while-shopping need a label**
Open question: how is a weighed item registered by phone?
Decision: it must be priced by a Weighing that has a Label (section scale).
Justification: "with the phone there's no way: either they weigh it and scan the label or the item doesn't get registered".
Depends: note on Weighing.

**D26 – One registration line per unit or weighing**
Open question: are repeated units one line with a quantity, or several lines (forum Q11)?
Decision: each scan or weighing is one Item Registration, with no quantity attribute.
Justification: Marisa talks of "a set of lines scattered across the receipt". Per-unit lines also make voids and per-line prices (D8) simple.
Depends: Item Registration, Campaign Application "concerns" 1..* lines. [confirm with forum]

## Campaigns

**D10 – Campaigns spanning several lines across a campaign end**
Open question: does a multi-line campaign (3-for-2, cross) apply if some lines were registered after it ended (forum Q3.2)?
Decision: it applies only if every line it concerns was registered within [start, end].
Justification: this extends D8 consistently. Each line is judged at its own registration time.
Depends: Campaign Application "concerns", note. [confirm with forum]

**D11 – No stacking: the most advantageous campaign applies**
Open question: what if several campaigns apply to the same item (forum Q3.2)?
Decision: each line is concerned by at most one Campaign Application (Discount or Card Credit), and it is the one most advantageous to the customer.
Justification: this is predictable for the customer, and complaints are already the main campaign problem.
Depends: note on Item Registration / Campaign Application. [confirmed by group]

**D12 – Personal coupons are instances of a coupon campaign**
Open question: what does a coupon discount, and can several be used per purchase (forum Q7.2, Q11)?
Decision: a Coupon Campaign issues Coupons to a Customer Card. The discount applies to the products the campaign covers. Several coupons may be redeemed in one purchase (subject to D11).
Justification: Marisa lists the coupon as one of the five campaign types, "issued to a specific person". The transcript does not limit coupons per purchase.
Depends: Coupon Campaign, Coupon (expiryDate), Coupon–Customer Card, Discount–Coupon. [confirm with forum]

**D13 – A cancelled purchase releases a single-use coupon**
Open question: how can "single use" coexist with "must become available again"?
Decision: Discount "redeems" 0..1 Coupon, and a coupon may be linked to several Discounts. At most one of those may belong to a purchase that is not cancelled.
Justification: the history of the failed use is kept, and the coupon is usable again (the "lady" case).
Depends: Discount–Coupon multiplicities, note on Coupon, Purchase State.

**D14 – Card credit is a campaign effect, not a discount**
Open question: how should type 5 be modelled so that its value "never" enters the total?
Decision: Campaign Application has two kinds, Discount (lowers the total) and Card Credit (credited to the Customer Card). Card Credit therefore requires an identified card.
Justification: "the same campaign from the designer's view, but the effect is of a different nature".
Depends: Campaign Application, Discount, Card Credit, Card Credit–Customer Card.

**D28 – Campaigns are chain-wide; their creator is not modelled**
Open question: who creates campaigns, and do they apply per store (forum Q5)?
Decision: Campaign has no link to Store, and there is no campaign-manager entity (the role belongs to the use case model as an actor).
Justification: the transcript is silent on both points, and the domain does not need them.
Depends: Campaign. [confirm with forum]

## Payments

**D15 – Accumulated card balance is used as a payment instrument**
Open question: how is the credited balance "used on a later purchase"?
Decision: Card Balance Payment "is a" Payment and "draws on" the Customer Card.
Justification: this keeps D14 symmetric: the value never lowers a total, either when earned or when spent. It also matches the fixed order in D16.
Depends: Card Balance Payment. [confirmed by group]

**D16 – Fixed order of payment instruments**
Open question: the order "isn't irrelevant" but lives in a manual (forum Q3.3, Q6).
Decision: return vouchers first, then meal card, then card balance, then bank card/cash. Payment.sequence records the order.
Justification: the voucher remainder is lost, so the voucher goes first to minimise the loss. The meal card goes next because it is restricted to food.
Depends: Payment.sequence, note on Payment. [confirmed by group]

**D17 – The meal card pays only food lines, net of their own discounts**
Open question: who enforces the food restriction, and how are mixed discounts split (forum Q3.4)?
Decision: the checkout domain enforces it. The meal card amount is at most the value of the not-voided Food Product lines after the discounts on those same lines.
Justification: "that card is only good for the milk". Charging each discount to the discounted line follows D11 (one application per line).
Depends: Food Product, Meal Card Payment note. [confirm with forum]

**D18 – Returns and voucher issuing are out of scope**
Open question: does the system issue return vouchers (forum Q9, Q3.5)?
Decision: the Return Voucher arrives already issued (with an amount). It is single-use, and the unused remainder is lost. Returns are not modelled.
Justification: the transcript describes the voucher only as a payment instrument.
Depends: Return Voucher, Voucher Payment. [confirmed by group]

**D19 – A cancelled purchase restores coupon, balance and voucher**
Open question: what happens to a coupon, balance, voucher and partial payments when a purchase is cancelled (forum Q3.5, Q11)?
Decision: state "cancelled". Coupon, card balance and return voucher become available again. Money already paid by bank card or cash follows the store's normal refund process, outside the system.
Justification: this extends Marisa's coupon rule to the other stored-value instruments, because the purchase did not go through.
Depends: Purchase State, notes on Coupon, Return Voucher, Payment. [confirmed by group]

## Customer card

**D23 – A card belongs to exactly one person; shared cards are not supported**
Open question: family cards: support them or prevent them ("there is no position")?
Decision: Person 1 owns Customer Card. There is no multi-user account and no Phone entity linking several users to a card.
Justification: this is the stated official rule ("the card belongs to one person"). Multi-user accounts are the open-ended option Marisa warns about.
Depends: Person–Customer Card. [confirmed by group]

**D24 – Scan-while-shopping requires an identified card; other modes do not**
Open question: must the customer be identified (forum Q7.3, Q10)?
Decision: a Scan-While-Shopping Purchase must be identified by a Customer Card. Assisted and self-checkout purchases may be anonymous (0..1).
Justification: validation needs the customer's history (D5). Nothing in the transcript requires a card in the other modes.
Depends: Purchase–Customer Card 0..1, note on Purchase. Phone mode: [confirmed by group]. Optional in the other modes: [confirm with forum]

## Stores, stations, staff, voids

**D21 – Station modes**
Open question: how to model "same machine, different mode", and who switches the mode (forum Q5)?
Decision: a Self-Service Station "works in" exactly one Station Mode (Self-Checkout or Phone-Payment) at a time. Mode history and the person who switches are not modelled.
Justification: "it's the same machine, what changes is the mode". A store decision that "varies from day to day" belongs to the use cases.
Depends: Self-Service Station, Station Mode and its two "is a", note on Purchase. [confirm with forum]

**D25 – Queues and display**
Open question: which queues does the domain need?
Decision: a Queue "leads to" 1..* Stations. A Display (only where there is a single queue) "calls the next customer from" it to a Station number. The operator's "free" signal is an event (use case), not an entity, and customers waiting in the queue are not identified.
Justification: transcript: single queue with display in large stores, own queues in small ones, a queue for getting a self-service station.
Depends: Queue, Display, Station.number. [confirm with forum]

**D27 – Staff roles**
Open question: which staff roles appear?
Decision: Operator, Support Assistant and Supervisor "is a" Staff Member. Any Staff Member may perform a Validation. A Support Assistant watches 1..* Self-Service Stations ("six or eight" is practice, not a rule).
Justification: these are the only roles named in the transcript.
Depends: Staff Member and its subtypes, Validation–Staff Member.

**D20 – Voids above a threshold need a supervisor, in every mode**
Open question: is the threshold on the item or the total, which modes does it apply to, and what does the report contain (forum Q8, Q11)?
Decision: a Void cancels one line, and the line stays recorded. A Supervisor authorisation is needed when the voided line's value is above a store threshold, in all three modes (at the station). The monthly report is derived from the Voids (date, line, supervisor).
Justification: a shared rule across modes (D3). "It's not the operator who decides."
Depends: Void (voidedAt), Void–Item Registration, Void–Supervisor 0..1, note. [confirm with forum]

## Gaps closed after the use case model (D51-D55)

These entries continue the numbering of `use_cases/requirements_decisions.md` (D31-D50). Each one closes a
value that a use case step needs but the domain model did not hold (see the "domain model gaps" section
of that file).

**D51 – Product has a current price**
Open question: UC09 step 2 and UC05 step 2 take "the Product's price at that moment", but Product had no price.
Decision: `Product.price : Decimal` holds only the current price. No price history is modelled: the price that
was in force is kept as a snapshot in Item Registration.unitPrice (D8) and Weighing.price (D9).
Justification: "the price that counts is the one at the moment the item was registered" implies that a product
has a price that can change; the snapshots already answer every past-price question (the Ficha 02 e-commerce
lesson: the price at purchase time lives on the line).
Depends: Product (price), notes on Product and Item Registration, UC05, UC09. [confirm with forum]

**D52 – Weighed Product is a kind of Product**
Open question: UC09 alternative 2 and exception 3 depend on whether "the product is sold by weight"; nothing in
the model said so.
Decision: Weighed Product "is a" Product, like Food Product. A Weighing is "of" a Weighed Product (the
association moved from Product). For a Weighed Product, Product.price is the price per unit of weight, and
Weighing.price = weight x that price at weighedAt. A product may be both a Food Product and a Weighed Product
(cheese).
Justification: Alberto names "weighed items. Fruit, vegetables, cheese from the counter" as a kind of item with
its own registration rules ("either they weigh it and scan the label or the item doesn't get registered").
The course prefers a typed entity linked by "is a" to a flag attribute (T03 rules), and it is the same pattern
as Food Product (D17).
Depends: Weighed Product, Weighing–Weighed Product, note on Product, UC05, UC09. [confirm with forum]

**D53 – The void threshold is an attribute of Store**
Open question: UC14 step 2 and UC15 compare the voided line with "the store's threshold" (D20), which had no
place in the model.
Decision: `Store.voidThreshold : Decimal`. A void of a line whose unitPrice is above it needs a Supervisor's
authorisation. No value is fixed by the model.
Justification: Alberto: "above a certain amount I have to go over with my card and authorise it". D20 already
decided that the threshold is set by the store; a single number needs no entity of its own.
Depends: Store (voidThreshold), note on Void, UC14, UC15. [confirm with forum]

**D54 – Values of Cross Discount, Coupon Campaign and Card Credit Campaign**
Open question: only Percentage Discount and Quantity Discount had value attributes; UC09 cannot compute the
other three, and UC13 asks the Campaign Manager for values with no place in the model.
Decision: Cross Discount and Coupon Campaign have `percentage : Decimal` (percentage off the covered product's
line). Card Credit Campaign has `amount : Decimal` (value credited to the card for each covered line).
Justification: the transcript gives the value of a discount only as a percentage ("twenty per cent off that
yoghurt"), so the cross discount ("the discount on product A") and the coupon reuse that form instead of
inventing another. For the fifth kind "the campaign value is credited to their card", i.e. a value, which the
scenarios (S4, S12) express as a fixed amount. A fixed-amount discount or a percentage card credit would need a
client answer.
Depends: Cross Discount, Coupon Campaign, Card Credit Campaign attributes, note on Campaign Application, UC09,
UC13. [confirm with forum]

**D55 – Clean history lowers the base percentage by a store-configured amount**
Open question: UC08 step 4 reduces the chance of a check by the card's clean history (D5), but Validation Policy
had no parameter saying by how much.
Decision: `ValidationPolicy.cleanHistoryReduction : Decimal`: percentage points subtracted from basePercentage
for each past Validation of the card that ended with No Discrepancy, never below 0. It is set by the store with
the other parameters (UC12); no value is fixed by the model. Forced checks are not affected.
Justification: Hélder: "someone who's been checked five times without a problem should be bothered less", and
the trade-off "is not going to be settled by you" (D6), so the size of the reduction must be a business
parameter, not a fixed rule.
Depends: Validation Policy (cleanHistoryReduction), note on Validation Policy, UC08, UC12. [confirm with forum]

Entity-name check: after D51-D55 every entity named in `use_cases/use_case_specifications.md` and
`use_cases/scenarios.md` exists in `domain.puml` with the same name (actors such as Campaign Manager and Loss
Prevention Manager are not domain entities, D28, D31). No other mismatch was found.

## Left out on purpose
- **The system, screens, buttons, the "payment on hold" message**: these belong to the solution, not the problem (T03 rules).
- **Belt**: no information about it is needed. Only the checkout scale matters.
- **Section / counter**: Section Scale is enough to distinguish where a label comes from.
- **Receipt** (including the "line at the bottom" for card credit): an output derived from lines, applications and payments (D2).
- **Monthly void report**: derived from Void (D20).
- **Card balance, purchase total, customer history** as attributes: derived (D2, D5).
- **Phone (device)**: the purchase is identified by the card (D23, D24), and the device carries no domain data.
- **Item returns, refunds, the manual refund of the August case**: out of scope (D18, D19).
- **Multi-user / family account**: explicitly not supported (D23).
- **Campaign manager / who switches station modes**: these are actors, not domain entities (D21, D28).
- **Barcodes / product codes, product names, store size**: the transcript does not say the system needs them (the store's queue set-up covers store size, D25).
- **Supervisor's card**: it is only the means of identifying the Supervisor, and the Void–Supervisor link records who authorised.
- **Payment terminal / bank / meal-card issuer**: external, and nothing about them is needed.
