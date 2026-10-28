# Decision log: Use Case Model (Phase 1)

Continues `../decisoes_dominio.md` (D1-D30). Model files: `ucd_geral.puml` and the sub-diagrams
`ucd_lojas.puml`, `ucd_caixa_assistida.puml`, `ucd_self_checkout.puml`, `ucd_scan_telemovel.puml`,
`ucd_validacao.puml`, `ucd_campanhas.puml`, `ucd_correcoes.puml`; scenarios in `cenarios.md`;
specifications in `especificacoes_use_cases.md`.
Tags: **[confirm with forum]** = default that depends on a client answer on the forum.

## Actors

**D31 – Actors chosen with the balanced rule**
Open question: which external parties are actors (devices, payment terminal, bank, meal-card issuer)?
Decision: actors are Customer, Staff Member (generalising Operator, Support Assistant, Supervisor),
Loss Prevention Manager and Campaign Manager. Display, scales, phone app and stations are part of the
system; payment terminal, bank and meal-card issuer are not actors.
Justification: T07 balanced rule: only external parties that need functionality from the system. The
staff generalisation mirrors D27 ("a staff member comes to check").
Depends: all diagrams, actor list in `cenarios.md`.

**D32 – Campaigns are defined by a Campaign Manager**
Open question: who defines campaigns? The transcript does not say (forum Q5).
Decision: a Campaign Manager actor (the role of Marisa, "loyalty programme and campaigns") defines
campaigns of the five kinds, chain-wide (D28), including the issuing of personal coupons.
Justification: Marisa is the one who describes and "designs" campaigns ("from the point of view of whoever
designs it"); the system cannot apply campaigns that nobody defines.
Depends: UC13, `ucd_campanhas.puml`. [confirm with forum]

**D33 – The validation policy is configured by a Loss Prevention Manager**
Open question: who configures the selection rule, given Hélder vs Sandra ("it's not going to be settled
by you")?
Decision: a Loss Prevention Manager actor sets, per store (D6), the base percentage, the amount for forced
checks and the flagged products.
Justification: Hélder (loss prevention) asks to "be able to force a check under certain conditions", and
the transcript leaves the trade-off to the business, not to the system.
Depends: UC12, `ucd_validacao.puml`. [confirm with forum]

**D34 – Station mode is changed by a Supervisor, never during a purchase**
Open question: who switches self-service stations between modes, and when (forum Q5)?
Decision: the Supervisor changes the mode; the change is refused for a station that has a purchase in
state "open" or "awaiting validation" paid at it.
Justification: Alberto (checkout supervisor) says "in the afternoon we switch"; Sandra: "a store
decision". Switching mid-purchase would break the mode constraints of D3/D21.
Depends: UC02, exception (1). [confirm with forum]

**D35 – The Support Assistant has no use case of its own**
Open question: what does the support assistant do in the system?
Decision: "watching six or eight stations" is a physical activity with no system function in the
transcript. The Support Assistant appears only as a Staff Member who may validate purchases (UC11).
Justification: adding monitoring functions would be an unsupported addition (assignment section 8).
Depends: actor generalisation in `ucd_geral.puml`, `ucd_validacao.puml`. [confirm with forum]

**D36 – The monthly void report lists authorised voids and goes to loss prevention**
Open question: who receives the report, and which voids does it contain?
Decision: the Loss Prevention Manager obtains, per store and month, the Voids authorised by a Supervisor
(date, product, line value, kind of purchase, supervisor, totals).
Justification: Alberto: "above a certain amount I have to ... authorise it, and at the end of the month
there's a report on that"; "that" are the authorisations. The report is derived from Void (D2, D20).
Depends: UC17. [confirm with forum]

## Model structure

**D37 – One general diagram, six sub-diagrams; «include» for shared behaviour; no generalisation of
"Register purchase"**
Open question: how to keep the model readable and share what the three modes share?
Decision: the general diagram has seven coarse use cases, each detailed in a sub-diagram. "Register item"
and "Pay purchase" are separate use cases «include»d by the mode use cases. An abstract "Register
purchase" specialised by mode was rejected.
Justification: pricing, campaign and payment rules are the same in all modes (assignment section 2), so
they are written once. In scan-while-shopping the purchase is spread over three use cases and up to an
hour (start, scan, finish), so it is not one specialisation of a common flow.
Depends: all `.puml` files, UC03, UC04, UC07, UC08, UC09, UC10.

**D38 – «extend» only for validation and void authorisation**
Open question: are validation and supervisor authorisation part of the base flows?
Decision: "Validate purchase" extends "Finish scan-while-shopping purchase" at *validation* [purchase
selected by the store's Validation Policy]; "Authorise void" extends "Void item" at *authorisation*
[voided line value above the store's void threshold].
Justification: both add behaviour under a condition, with a different actor; the base use cases are
complete without them (most purchases are not checked, most voids are small). No other include/extend is
used.
Depends: `ucd_validacao.puml`, `ucd_correcoes.puml`, UC08, UC11, UC14, UC15.

## Campaigns, card and payment

**D39 – Card credit is credited only when the purchase is paid**
Open question: when does a Card Credit become usable balance?
Decision: Card Credits of a purchase count in the card's balance only once the purchase is "paid"; a
cancelled purchase never credits.
Justification: the credit is "to use on a later purchase"; a purchase that "didn't go through" (Marisa's
coupon case) must not leave value on the card. Balance stays derived (D2).
Depends: UC10 step 5, UC16 post-condition. [confirm with forum]

**D40 – The card may be identified at any moment before payment**
Open question: in assisted and self-checkout, must the card be shown at the start?
Decision: the card can be presented at the start or later, before payment; then the campaigns of all lines
are re-evaluated, each at its own registration time (D8, D10).
Justification: the transcript does not fix the moment; re-evaluating at registration times keeps the
"price at registration" rule.
Depends: UC03 and UC04 alternative (3). [confirm with forum]

**D41 – One scan-while-shopping purchase in progress per card**
Open question: two people using the same card on two phones at the same time ("nobody having decided")?
Decision: a card may have only one Scan-While-Shopping Purchase in state "open" or "awaiting validation".
Justification: consequence of D23 (one card = one person, shared cards not supported); it also keeps the
customer history (D5) and the station fetch (UC08 step 2) unambiguous.
Depends: UC06 exception (2). [confirm with forum]

**D42 – Coupons of the identified card are applied automatically**
Open question: does the customer have to present the coupon?
Decision: coupons are associated with the card (Marisa), so when the purchase is identified by a card,
its valid coupons (not expired, not used) are considered at registration time like any other campaign,
subject to D11.
Justification: no presentation step is described; the coupon is "issued to a specific person" and lives
on the card.
Depends: UC09 step 3. [confirm with forum]

**D43 – In scan-while-shopping, voids that need authorisation are done at the station**
Open question: how can a supervisor authorise a void made on the phone in the middle of the store?
Decision: on the phone the customer can void lines up to the threshold; above it, the void must be done at
the payment station, before paying, where a supervisor can come.
Justification: D20 places the authorisation "at the station"; the supervisor "goes over with the card".
Depends: UC14 exception (1), UC15 pre-condition. [confirm with forum]

**D44 – Only open purchases can be cancelled; by the customer or the operator**
Open question: who cancels, and in which states?
Decision: the Customer (station or phone) or the Operator (assisted checkout) cancels a purchase in state
"open". A purchase awaiting validation, paid, or sent to assisted checkout cannot be cancelled.
Justification: Marisa's case is a cancellation "because of a payment problem" before the purchase went
through. Cancelling during validation would let a customer escape the check and remove the evidence (D4);
cancelling a paid purchase would be a return (out of scope, D18).
Depends: UC16 pre-condition. [confirm with forum]

## Validation and checkouts

**D45 – Return to the assisted checkout: the operator starts the new purchase**
Open question: when is the new Assisted Purchase of D4 created?
Decision: the original purchase goes to "sent to assisted checkout" when the outcome is recorded (UC11);
the new Assisted Purchase is created by the Operator at the assisted checkout, identified by the same card
and linked to the Unexplained Discrepancy.
Justification: an Assisted Purchase needs its Operator ("registered by", 1), who is only known at the
checkout.
Depends: UC03 alternative (4), UC11 alternative (2).

**D46 – Selection for validation happens once, at the station, before payment**
Open question: when is the rule applied (scanning goes on for up to an hour)?
Decision: when the station fetches the purchase (UC08 step 4), with all lines known, and only once.
Justification: forced checks depend on the amount and on the products in the basket, which are final only
at that point; "a message comes up, the payment is put on hold".
Depends: UC08 step 4, extension point *validation*. [confirm with forum]

**D47 – Weighing in the section is a use case of the Customer**
Open question: is the section scale part of the system, and who uses it?
Decision: yes, since the label must refer to a recorded Weighing (D9, D22). The actor is the Customer;
counter staff weighing cheese for a customer are not given a separate role.
Justification: the transcript says the item is "weighed in the section" without saying by whom; the price
at weighing time must be known to the system.
Depends: UC05. [confirm with forum]

**D48 – Data assumed to exist (no use case maintains it)**
Open question: who registers products, prices, food classification, stores, stations, queues, displays,
staff, the operator of each checkout, the void threshold, customer cards and return vouchers?
Decision: the transcript is silent; these are treated as data that already exist in the system (loaded
outside the scope of this model), and appear only in pre-conditions.
Justification: the system cannot work without them, but no stakeholder described how they are maintained;
adding management use cases would be unsupported. Voucher issuing is already out of scope (D18).
Depends: conventions in `especificacoes_use_cases.md`, UC01, UC03. [confirm with forum]

**D49 – Payment split is computed once all instruments are known; refusals are alternative flows**
Open question: how is the fixed order (D16) applied when the customer offers several instruments, and what
if one is refused?
Decision: the system splits the total in the fixed order after the customer indicates the instruments;
a refused bank card or meal card returns to choosing another instrument; giving up leaves the purchase
open (it may then be cancelled).
Justification: the order "isn't irrelevant" and must not depend on what the customer offers first; the
external terminal is not an actor (D31).
Depends: UC10.

**D50 – "Free" signal: the operator decides, the system does not check the queue**
Open question: what does the system do when the operator signals that they are free?
Decision: it calls the next customer to that checkout number, whenever the operator signals; it only
exists where the store has a single queue with a display.
Justification: "whoever decides they're free is the operator, not the system"; waiting customers are not
identified (D25).
Depends: UC01.

## Domain model gaps found while writing the use cases

The domain model was **not** changed. The use cases refer to the following values that have no place in
`../dominio.puml` yet; the group should decide whether to add them (proposal in brackets):
- Price of a Product at a given moment (UC05, UC09): Product has no price [`Product.price`: the current
  price is enough, because Item Registration.unitPrice and Weighing.price keep the snapshot, D8/D9].
- Product sold by weight (UC09 alternative 2 and exception 3) [`Product.soldByWeight : Boolean`].
- Store void threshold (UC14, UC15, D20) [`Store.voidThreshold : Decimal`].
- Value of Cross Discount, Coupon Campaign and Card Credit Campaign (UC13): only Percentage Discount has a
  value attribute [a value / percentage attribute on each].
- How much clean history reduces the selection chance (UC08 step 4, D5/D6) is not a Validation Policy
  attribute [leave as a fixed rule, or add a parameter].

## Left out on purpose
- **Log-in of operators and staff, and assigning an operator to a checkout**: not in the transcript;
  "Operator operates the Assisted Checkout" is a pre-condition (D48).
- **Maintenance of products, prices, stores, stations, queues, displays, staff, cards, thresholds**: data
  assumed to exist (D48).
- **Changing or ending a campaign early, campaigns per store**: not described; a campaign ends at its end
  (D28).
- **Assignment of self-service stations from their queue**: "the queue there is for getting a station";
  nothing for the system to do.
- **Monitoring by the support assistant**: physical activity, no system function (D35).
- **Receipt printing** (including the card-credit line "at the bottom"): output detail; the customer is
  informed of the card credit in UC10.
- **Refunds of bank card / cash, the manual refund of the August case, item returns, voucher issuing**:
  outside the system (D18, D19).
- **Family / multi-user cards**: not supported (D23, D41).
- **Abandoned phone purchases (customer leaves without paying or cancelling)**: no timeout or clean-up is
  described; the purchase stays open until cancelled.
- **Unknown or unreadable product codes**: not mentioned in the transcript; the item simply is not
  registered.
- **Customer consulting balance, coupons or history**: not described.
- **Numeric values** (percentages, amounts, thresholds): set by the business, never fixed by the model
  (D6, D7).
- **Screens, buttons, the wording of messages**: user interface, not requirements (T05).
