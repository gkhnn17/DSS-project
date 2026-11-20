# Decision log: Phase 2 (design)

Specification for this phase: the transcript (Appendix A of the assignment) complemented by the scope restriction
(«Scope Restriction», 2026/2027). Phase 1 decisions D1-D55 stay in `../1_Requirements/` and are not rewritten;
when a Phase 2 decision changes one of them, it says so ("revises Dx").
Each entry: open question, decision, justification, what in the model depends on it.

Forum status (snapshot 2026-11-16 and later posts up to 08/11): no answer from the teaching team to any question;
every point below is therefore our own decision.

## A. Fixed by the scope restriction (recorded, not chosen)

**D56 – Only the scan-as-you-shop mode is designed**
Given: "The design and implementation phases address exclusively the scan-as-you-shop mode". Assisted and
self-checkout purchases, stations in self-service mode, queues and the operator are not in the design
(see `exclusions.md`). Depends: every Phase 2 diagram; `ScanPurchase` is the only kind of purchase.

**D57 – Every purchase is associated with a customer card**
Given: "Each purchase in this mode must be associated with a customer card." This confirms D24 for this mode.
A purchase is created only for an existing card (`IPurchases.startPurchase(cardId, storeId)`).
Depends: `ScanPurchase.card` (1), `startPurchase`, sequence "Register item".

**D58 – The customer card balance is a payment instrument**
Given: "the balance accumulated on the customer card is a payment instrument". This confirms D15 and settles
forum question 21/10 (balance as discount or as payment). Depends: `CardBalanceInstrument`, `IPayments.pay`.

**D59 – A purchase marked not acceptable can no longer be paid; its record is kept**
Given: "A purchase that is not acceptable can no longer be paid for, but its record is kept; what happens next is
outside the scope." Revises D4 for this phase: there is no new assisted purchase; the purchase goes to the final
state `NotAcceptable`. Depends: state machine `stm_purchase.puml`, `IVerification.markNotAcceptable`.

## B. Decided by the group

**D60 – The verification probability depends on the number of paid purchases in this mode (revises D5, D55)**
Open question: what does "the number of purchases the customer has already made in this mode" count?
Decision: the number of purchases of the card in state `Paid` (scan-as-you-shop). Verifications and their outcomes
are not counted. A purchase marked not acceptable is never paid, so it never counts.
Justification: the scope restriction counts purchases, not verifications; D5 (only clean validations) does not
match that wording any more. "Made" is read as "completed", i.e. paid.
Depends: `IPurchases.countPaidPurchases(cardId)`, `VerificationStrategy.mustVerify`, sequence "Pay for purchase".

**D61 – Probability formula: p(n) = max(pMin, pBase × f^n), parameters per store**
Open question: the scope restriction fixes only that the probability "decreases" with n.
Decision: `DecreasingProbabilityStrategy` with three store parameters: pBase (probability for a first purchase),
f (0 < f < 1, factor per previous purchase) and pMin (floor, > 0). A random number r in [0,1) is drawn;
the purchase is verified if r < p(n). The model fixes no values (keeps the idea of D6).
Justification: strictly decreasing until the floor, simple to explain and to test (inject the random source). The
floor keeps a small chance of checking regular customers, so loyal customers are never exempt forever.
Depends: `DecreasingProbabilityStrategy`, `VerificationParameters`, `IVerification.decide`.

**D62 – The verification rule is a replaceable strategy**
Open question: Hélder also wants forced checks (amount, problematic items, incident history); not required now.
Decision: `IVerification` uses a `VerificationStrategy` interface. Only `DecreasingProbabilityStrategy` is
implemented. Other rules would be new strategies (or a composite of strategies) without changing the caller.
Justification: same open/closed reasoning that the scope restriction asks for campaigns and payment instruments.
Depends: `VerificationStrategy`, `extension_points.puml`.

**D63 – The customer can remove a registered item before starting payment; the line is kept as cancelled (revises D20, D43)**
Open question: forum 07/11 (Paulo): can a wrongly scanned item be removed? The scope restriction does not mention it.
Decision: while the purchase is `Open`, the customer can cancel one of its lines. The line is not deleted: it is
marked cancelled (with the time) and no longer counts for the total or for campaigns. No supervisor, no threshold.
After payment starts, lines cannot be cancelled.
Justification: in this mode the customer scans while walking, so mistakes (double scan, item put back) are expected,
and Register item is not usable without a way to correct them. Keeping the line gives the staff member the full
history during verification. Supervisor authorisation (D20) belongs to the station modes, which are excluded.
Depends: `ItemLine.cancelledAt`, `IPurchases.cancelLine`, sequence "Register item" (alt), state machine.

**D64 – A label identifies the product and, for a weighed item, carries its weight and price (answers forum 28/10)**
Open question: forum 28/10 (André): should there be a Label class linked to Product?
Decision: the catalog resolves a label code to a `LabelReading` (product, unit price, optional weight). A
non-weighed product's label is its product code and gives the current price (D8, D51). A weighed item's label
was printed by the section scale and gives the weight and the price at weighing time (D9, D30). Product gets a
`code`; this revises the Phase 1 exclusion "barcodes / product codes".
Justification: "reading the label allows the system to identify the item and obtain its price, whether or not it
has been weighed". One operation covers both cases, so Register item has a single flow.
Depends: `ICatalog.readLabel`, `LabelReading`, `Product.code`, `WeighingLabel`.

**D65 – "Buy 2, pay 1": pairs of lines of the same product; the cheaper line of each pair is free**
Open question: which lines form the pair, and which one is free (weighed items can have different prices)?
Decision: the campaign covers a set of products and has a validity period [start, end]. For each covered product,
the not-cancelled lines registered inside the validity period are sorted by price (highest first) and taken in
pairs; in each pair the cheaper line is free. An odd line is paid normally. Two different products are never paired.
Justification: "buy 2, pay 1" names the same item twice; ordering by price gives the customer the larger
discount for the first pair without making the shop pay for the more expensive item. Validity per line keeps D8/D10.
Depends: `BuyTwoPayOne.evaluate`, `Campaign.isValidAt`, sequence "Register item" (recompute).

**D66 – Campaigns are evaluated on the whole purchase and recomputed after every change**
Open question: when is the discount computed, given that campaigns may concern several lines?
Decision: after each registration or cancellation, `ICampaigns.evaluate(purchase)` recomputes all effects from the
lines (each line judged at its own registration time, D8/D10). The result is frozen when payment starts. D11 still
holds: a line is used by at most one campaign effect, the most advantageous one.
Justification: the customer sees the running total in the app; cancellations (D63) can break a pair, so effects
cannot be accumulated incrementally. A whole-purchase input is also what the other campaign types need
(quantity, cross discount, coupon of the card).
Depends: `Campaign.evaluate(PurchaseView)`, `CampaignEffect`, `ICampaigns`.

**D67 – Campaign effects: a discount lowers the total; other effects act when the purchase is paid**
Open question: how do campaigns that do not lower the total (card credit) fit without changing existing code?
Decision: `Campaign.evaluate` returns `CampaignEffect` objects. Each effect has `discount()` (amount off the
total) and `onPaid(card)` (called once when the purchase is paid). `Discount` implements the first and does nothing
in the second. A future `CardCredit` would return 0 and credit the card in `onPaid` (keeps D14 and D39).
Justification: answers forum 08/11 (Luís): card-credit campaigns are not implemented in this phase; the balance used
for payment is assumed to have been credited earlier. The hook exists now, so adding them changes nothing.
Depends: `CampaignEffect`, `Discount`, `IPayments.pay` (calls `onPaid`), `extension_points.puml`.

**D68 – Combined payment: the customer chooses how much balance to use; the bank card pays the rest (revises D16)**
Open question: order and split between card balance and bank card ("in a manual, not in the system").
Decision: the customer enters a balance amount b with 0 ≤ b ≤ min(available balance, total). The bank card pays
total − b (nothing if b = total). The balance is reserved first, then the bank card is charged; if the bank
refuses, the reservation is released and the purchase stays `ReadyToPay`. Each instrument used is recorded as a
`PaymentPart` (instrument, amount).
Justification: leaves the choice to the customer (the scope restriction allows "a combination of the two") and
avoids partial payments if the bank fails. The generic list of parts works for future instruments.
Depends: `IPayments.pay(purchaseId, List<PaymentRequest>)`, `PaymentInstrument`, sequence "Pay for purchase".

**D69 – Payment instruments implement one interface with a limit**
Open question: how do cash, meal card and return voucher fit later (scope restriction §3)?
Decision: `PaymentInstrument` has `maxAmount(purchase)`, `charge(amount)` and `release(amount)`.
`CardBalanceInstrument` (max = balance) and `BankCardInstrument` (via the external `IBankGateway`) are implemented.
Meal card would limit `maxAmount` to the food lines (D17), the return voucher to its amount (remainder lost, D18),
cash to no limit. `IPayments` only checks that the parts add up to the total and each is within its maximum.
Justification: the rules that differ between instruments live inside each instrument, so `IPayments` does not change.
Depends: `PaymentInstrument` and implementations, `extension_points.puml`.

**D70 – Missing items during verification are normal registrations**
Open question: how does "the customer must be able to register missing items" work?
Decision: while the purchase is `UnderVerification`, the customer uses the same Register item operation; the line
gets the price and campaigns in force at that moment (keeps D29, but the customer, not the staff member, registers).
Cancelling lines is not allowed in this state (D63).
Justification: the scope restriction gives the action to the customer; reusing Register item avoids a second flow.
Depends: `IPurchases.registerItem` (allowed in `Open` and `UnderVerification`), state machine, sequence "Verify purchase".

**D71 – The outcome records the staff member and the time; it has two values**
Open question: Phase 1 had three outcomes (no / small / unexplained discrepancy, D7).
Decision: `Verification` records staff member, start and end time and outcome `Compliant` or `NotAcceptable`, as
the scope restriction states. "Small discrepancy" becomes: the customer registers the missing items, then the
staff member marks the purchase compliant.
Justification: "the staff member must be able to mark the purchase as compliant or as not acceptable, and the
outcome is recorded". Recording who verified keeps the Phase 1 link Validation–Staff Member.
Depends: `Verification`, `IVerification.markCompliant / markNotAcceptable`.

**D72 – Payment is started once; the list is then closed**
Open question: can the customer go back to shopping after starting payment?
Decision: `startPayment` freezes the purchase: state `ReadyToPay` (not selected) or `UnderVerification` (selected).
Only verification can add lines after that (D70). The selection is done once per purchase (keeps D46).
Justification: otherwise a customer could start payment, see that they were not selected, and keep scanning,
which defeats the verification.
Depends: state machine, `IPayments.startPayment`.

**D73 – One open purchase per card; abandoned purchases are not handled**
Open question: what happens to a purchase that is never paid?
Decision: a card has at most one purchase that is not `Paid` or `NotAcceptable` (keeps D41). Cancelling or expiring
an abandoned purchase is outside the scope restriction and is not designed (D44, D19 not carried over).
Justification: none of the three functionalities covers it; the scope restriction excludes "all other functionalities".
Depends: `IPurchases.startPurchase` (refuses a second open purchase), `exclusions.md`.

**D74 – Architecture: one facade per subsystem, called by a business-layer facade**
Open question: how to decompose the system.
Decision: layers UI → business layer (`ISupermarketLN`) → subsystems `Purchases`, `Catalog`, `Campaigns`,
`Payments`, `Verification`, `CustomerCards`, each with its own interface and data access (DAO). Only `Payments` talks
to the external bank. Subsystems call each other only through their interfaces.
Justification: each subsystem follows one functionality or one extension point of the scope restriction, so a new
campaign or instrument only touches its own subsystem.
Depends: `architecture.puml`, `interfaces.md`.
