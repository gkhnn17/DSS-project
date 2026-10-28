# Use Case Model (Phase 1, step 2): scenarios, actors and use cases

Source of truth: transcript in Appendix A of the assignment. Capitalised names such as Item
Registration or Customer Card are the entities of `../domain.puml`. Decisions D1-D30 and D51-D55
are in `../domain_decisions.md`; D31-D50 (use case model) are in `requirements_decisions.md`.
Diagrams: `ucd_general.puml` (general) and six sub-diagrams `ucd_*.puml` (several diagrams, one model).
Specifications: `use_case_specifications.md`.

## 1. Scenarios

Concrete, informal descriptions from the point of view of the people involved. Names are invented; the
situations come from the transcript (quoted part in brackets) or from a recorded decision.

**S1 – Assisted checkout in a large store.** Rita, an operator at checkout 4 of the Braga store, finishes
taking a payment and presses the button that says she is free. The display above the single queue sends
the next person, Mr. Costa, to number 4. Rita starts a new purchase, Mr. Costa shows his customer card,
and Rita scans his items. The yoghurt has 20% off this week and the discount appears at once. Rita
weighs his apples on the checkout scale. When everything is registered, Mr. Costa pays the total with
his bank card. ("when an operator becomes free they press a button and the display sends the next
person to that number"; "the checkout has a scale and the person weighs it right there")

**S2 – Assisted checkout in a small store, with a small void.** In the small Guimarães store there is no
display; Paulo picks a queue himself. He has no customer card. The operator, Nuno, scans six bottles of
water that Paulo put on the belt in different places; the "buy three, pay for two" campaign applies to
them even though the lines are scattered. Paulo changes his mind about a pack of gum, and Nuno takes it
off: the value is low, so no authorisation is needed. The line stays recorded but no longer counts.
Paulo pays in cash. ("a set of lines that can be scattered across the receipt"; "if the customer
changes their mind about an item, fine, it's taken off")

**S3 – Void above the threshold at an assisted checkout.** At Rita's checkout, Inês decides not to take
an expensive bottle of whisky that is already registered. Its value is above the store's limit, so the
system asks for authorisation. Alberto, the checkout supervisor, comes over, identifies himself with his
card and authorises the void. Later the same day, Alberto refuses another customer's request to void an
expensive coffee machine, so that line stays and keeps counting. ("above a certain amount I have to go over with my card and authorise it
... It's not the operator who decides")

**S4 – Self-checkout with a label, a station scale, card credit and a voucher.** Tiago starts a purchase
at self-service station 12, which is working in self-checkout mode. Earlier he had the cheese weighed at
the cheese counter, and the section scale printed a label with the code and the price. He scans his
items and the cheese label, and weighs the bananas on the station's scale. He identifies his customer
card. The olive oil is in a campaign that credits 1 EUR to the card: the full price is charged and the
credit is shown apart. He pays with a return voucher of 8 EUR and the rest with his bank card. Carla,
the support assistant, watches stations 10 to 17 during this time. ("at self-checkout it works too";
"the campaign value is credited to their card, to use on a later purchase"; "a support assistant
watching six or eight stations")

**S5 – Self-checkout with a meal card and a refused card.** Sofia, at self-service station 15 (which has
no scale), scans milk, bread and detergent. She tries to register loose pears without a label and is
told to weigh them in the section first; she gives up on the pears. She wants to pay everything with her
meal card, but the system tells her the meal card can only pay for the milk and the bread. Her bank card
is then refused, so she pays the detergent in cash. ("that card is only good for the milk. The rest is
paid some other way"; "not for what they've got")

**S6 – Scanning while shopping, normal case.** Joana arrives at the Braga store, opens the app on her
phone and starts a purchase with her customer card. She scans items as she takes them from the shelves
and puts them straight into her bag. She weighs tomatoes on the section scale and scans the label. She
tries to scan loose onions without weighing them and the item is not registered. She takes a yoghurt
off her purchase on the phone (a low value). Forty minutes later she goes to station 21, working in
phone-payment mode; the station fetches her purchase, which is not selected for checking. She pays part
with the balance credited to her card by an earlier campaign and the rest by bank card. ("nothing gets
scanned again"; "either they weigh it and scan the label or the item doesn't get registered")

**S7 – Price and campaigns at registration time.** Manuel weighs cheese at 19:40 and scans coffee at 19:55,
while the coffee campaign runs until 20:00. He scans two bottles of water before 20:00 and a third at
20:05; the "three for two" campaign ended at 20:00. He pays at 20:10. The coffee keeps its discount, the
cheese keeps the 19:40 price, and the water campaign does not apply because one of its lines was
registered after the campaign ended. ("the price that counts is the one at the moment the item was
registered"; "it goes with the price at that time"; D10)

**S8 – Validation with no discrepancy.** Hugo finishes his phone purchase at station 22. The store's rule
selects his purchase (random part of the rule). A message says that a staff member will check his items,
and payment is put on hold. Carla, the support assistant, comes over, compares the bag with the
registered lines, and everything is in order. The validation is recorded as "no discrepancy" and Hugo
pays. ("the payment is put on hold, and a staff member comes to check"; "the incident is recorded as no
discrepancy")

**S9 – Validation with a small discrepancy.** Marta's phone purchase contains razor blades, a product the
store flags as problematic, so a check is forced. Carla finds a chocolate bar that was not scanned, in
good faith. She registers it there, the total is recalculated and Marta pays. ("the staff member adds it
right there, the total is recalculated and off they go")

**S10 – Validation with an unexplained discrepancy.** Rui's phone purchase is above the store's amount
for forced checks. Pedro, an operator, checks it and finds many items in the bag that were never
registered. He records an unexplained discrepancy. Rui's purchase stays recorded as evidence but cannot
be paid. Rui goes to Rita's assisted checkout, where Rita starts a new purchase linked to that check and
scans every item again; Rui pays there. ("the purchase goes back and has to be scanned again from the
start, item by item, at an assisted checkout"; "It stays ... it's the evidence")

**S11 – Configuring the selection rule.** Hélder, from loss prevention, sets the selection rule for the
Braga store: a base percentage of purchases checked at random (lower for customers with clean past
checks), checks forced above a given amount, and razor blades and spirits flagged as problematic.
("I want it to start weighing the customer's history"; "force a check ... above a certain amount, or
when the basket has things we know are problematic")

**S12 – Defining the five kinds of campaign.** Marisa defines this week's campaigns: 20% off a yoghurt;
"buy three, pay for two" on water; a cross discount on biscuits for customers who also buy coffee; a
card credit of 1 EUR for olive oil; and a coupon campaign that issues a personal coupon for detergent,
with an expiry date, to a list of customer cards. ("We have five different ways of changing what the
customer pays")

**S13 – Coupon, payment problem and cancellation.** Dona Amélia has a personal detergent coupon on her
customer card. At a self-checkout station she identifies her card and scans the detergent: the coupon
discount is applied. She pays part with a return voucher, but her bank card fails repeatedly and she
cancels the purchase. The coupon and the voucher become available again; the card credit of the olive
oil she had scanned is not credited. Next week she uses the coupon. ("If it's single-use and the
purchase didn't go through, it has to become available again")

**S14 – Several campaigns, expired coupon, voucher larger than the total.** At an assisted checkout,
Luís buys coffee and biscuits. The biscuits are covered both by a 20% discount and by the coffee cross
discount; only the more advantageous one is applied. His coupon for detergent has expired and is not
applied. He pays with a 20 EUR return voucher; the purchase is 15 EUR, and the system tells him the
remaining 5 EUR is lost. ("If the next purchase costs less than the voucher, the rest is lost"; D11)

**S15 – Changing station mode.** At 14:00 Alberto switches stations 10 to 13 from self-checkout mode to
phone-payment mode. Station 12 still has Tiago's purchase in progress, so that station keeps its mode;
Alberto switches it once Tiago has paid. ("in the afternoon we switch. It's the same machine, what
changes is the mode")

**S16 – Void above the threshold in scan-while-shopping.** Joana scanned an expensive bottle of wine and
puts it back on the shelf. On the phone, the system tells her this void must be done at the payment
station. At station 21, before paying, she asks for the void and Alberto authorises it. (D20, D43)

**S17 – Card rules and giving up on the phone.** Diogo tries to start a phone purchase with his mother's
customer card while she is shopping with it on her own phone: the system refuses because the card
already has a purchase in progress. Bruno, who has no customer card, cannot start a phone purchase and
uses self-checkout instead. Leonor starts a phone purchase, scans three items, gets a phone call and
cancels the purchase on her phone. ("The card belongs to one person"; D23, D24, D41)

**S18 – Monthly void report.** On the first day of November, Hélder obtains the report of the voids
authorised by supervisors in the Braga store during October. ("at the end of the month there's a report
on that")

## 2. Actors

Chosen with the balanced rule (T07): an actor is an external role that needs functionality from the
system (D31).

| Actor | One-line justification |
|---|---|
| Customer | Registers items (self-checkout, phone), weighs items in the section, pays, voids and cancels; the person the system serves in all three modes. |
| Staff Member | Any employee may check a scan-while-shopping purchase ("a staff member comes to check", D27). Generalises the three roles below. |
| Operator | Registers items at the assisted checkout and signals that they are free ("whoever decides they're free is the operator"). |
| Support Assistant | Watches self-service stations; as a Staff Member performs validations; has no function of its own (D35). |
| Supervisor | Authorises voids above the threshold ("I have to go over with my card") and switches station modes (D34). |
| Loss Prevention Manager | Configures the validation selection rule (Hélder: "I want to be able to force a check", D33) and receives the monthly void report (D36). |
| Campaign Manager | Defines the five kinds of campaign (Marisa's role, D32). |

Not actors: display, section scale, checkout scale, phone app and stations (parts of the system);
payment terminal, bank and meal-card issuer (external, but they need nothing from the system) (D31).

## 3. Use cases

| Id | Use case | Primary actor(s) | Diagram | Scenarios |
|---|---|---|---|---|
| UC01 | Call next customer | Operator | ucd_stores | S1 |
| UC02 | Change station mode | Supervisor | ucd_stores | S15 |
| UC03 | Register assisted purchase | Operator, Customer | ucd_assisted_checkout | S1, S2, S3, S10, S14 |
| UC04 | Register self-checkout purchase | Customer | ucd_self_checkout | S4, S5, S13 |
| UC05 | Weigh item in section | Customer | ucd_self_checkout, ucd_phone_scan | S4, S6, S7 |
| UC06 | Start scan-while-shopping purchase | Customer | ucd_phone_scan | S6, S17 |
| UC07 | Scan item with phone | Customer | ucd_phone_scan | S6, S7, S17 |
| UC08 | Finish scan-while-shopping purchase | Customer | ucd_phone_scan | S6, S7, S8, S9, S10, S16 |
| UC09 | Register item («include»d) | (actor of the including use case) | ucd_assisted_checkout, ucd_self_checkout, ucd_phone_scan, ucd_validation | S1, S2, S4-S7, S9, S10, S12-S14 |
| UC10 | Pay purchase («include»d) | (actor of the including use case) | ucd_assisted_checkout, ucd_self_checkout, ucd_phone_scan | S1, S2, S4-S10, S13, S14 |
| UC11 | Validate purchase («extend»s UC08) | Staff Member | ucd_validation | S8, S9, S10 |
| UC12 | Configure validation policy | Loss Prevention Manager | ucd_validation | S11 |
| UC13 | Define campaign | Campaign Manager | ucd_campaigns | S12 |
| UC14 | Void item | Operator, Customer | ucd_corrections | S2, S3, S6, S16 |
| UC15 | Authorise void («extend»s UC14) | Supervisor | ucd_corrections | S3, S16 |
| UC16 | Cancel purchase | Customer, Operator | ucd_corrections | S13, S17 |
| UC17 | Obtain monthly void report | Loss Prevention Manager | ucd_corrections | S18 |

Coarse use cases of the general diagram (each detailed in one sub-diagram): Operate stations and queues
(UC01-UC02), Register and pay assisted purchases (UC03, UC09, UC10), Register and pay self-checkout
purchases (UC04, UC05, UC09, UC10), Register and pay scan-while-shopping purchases (UC05-UC10), Validate
scan-while-shopping purchases (UC11, UC12), Manage campaigns (UC13), Correct purchases (UC14-UC17).

## 4. Traceability: scenario -> use case -> domain entities

| Scenario | Use cases | Domain entities used |
|---|---|---|
| S1 | UC01, UC03, UC09, UC10 | Store, Queue, Display, Station, Assisted Checkout, Operator, Assisted Purchase, Customer Card, Item Registration, Product (price), Weighed Product, Checkout Scale, Weighing, Percentage Discount, Discount, Bank Card Payment, Purchase State |
| S2 | UC03, UC09, UC14, UC10 | Assisted Purchase, Operator, Item Registration, Quantity Discount, Campaign Application, Void, Cash Payment |
| S3 | UC03, UC14, UC15 | Assisted Purchase, Item Registration, Void, Supervisor, Store (voidThreshold) |
| S4 | UC05, UC04, UC09, UC10 | Section Scale, Weighed Product, Weighing, Label, Self-Service Station, Self-Checkout Mode, Self-Checkout Purchase, Checkout Scale, Customer Card, Card Credit Campaign (amount), Card Credit, Voucher Payment, Return Voucher, Bank Card Payment, Support Assistant |
| S5 | UC04, UC09, UC10 | Self-Service Station, Self-Checkout Purchase, Food Product, Weighed Product, Meal Card Payment, Bank Card Payment, Cash Payment |
| S6 | UC06, UC07, UC05, UC09, UC14, UC08, UC10 | Scan-While-Shopping Purchase, Customer Card, Item Registration, Section Scale, Weighed Product, Weighing, Label, Void, Phone-Payment Mode, Validation Policy, Card Balance Payment, Bank Card Payment |
| S7 | UC05, UC07, UC09, UC08, UC10 | Weighed Product, Weighing, Item Registration (registeredAt, unitPrice), Campaign (start, end), Percentage Discount, Quantity Discount, Campaign Application |
| S8 | UC08, UC11, UC10 | Validation Policy (cleanHistoryReduction), Validation, Support Assistant, No Discrepancy, Purchase State |
| S9 | UC08, UC11, UC09, UC10 | Validation Policy (flagged Product), Validation, Small Discrepancy, Item Registration |
| S10 | UC08, UC11, UC03, UC09, UC10 | Validation Policy (amountThreshold), Validation, Operator, Unexplained Discrepancy, Assisted Purchase, Purchase State |
| S11 | UC12 | Store, Validation Policy (basePercentage, cleanHistoryReduction, amountThreshold), Product |
| S12 | UC13 | Campaign, Percentage Discount, Quantity Discount, Cross Discount (percentage), Card Credit Campaign (amount), Coupon Campaign (percentage), Coupon, Customer Card, Product |
| S13 | UC04, UC09, UC10, UC16 | Coupon, Discount, Customer Card, Voucher Payment, Return Voucher, Card Credit, Purchase State |
| S14 | UC03, UC09, UC10 | Cross Discount, Percentage Discount, Campaign Application, Coupon (expiryDate), Voucher Payment, Return Voucher |
| S15 | UC02 | Supervisor, Self-Service Station, Station Mode, Self-Checkout Mode, Phone-Payment Mode, Purchase |
| S16 | UC14, UC08, UC15 | Scan-While-Shopping Purchase, Item Registration, Void, Supervisor, Store (voidThreshold) |
| S17 | UC06, UC07, UC16 | Person, Customer Card, Scan-While-Shopping Purchase, Purchase State |
| S18 | UC17 | Store, Void, Supervisor, Item Registration |
