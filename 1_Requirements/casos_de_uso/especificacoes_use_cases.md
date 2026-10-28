# Use case specifications (Phase 1, step 2)

Template of the course (T05, PL4 sheet): Use Case / Description / Scenarios / Pre-condition /
Post-condition / Normal flow / Alternative flows / Exception flows. Flow notation: `(n) [condition]
(step x)` with sub-steps `x.1, x.2 ...`; "Return to x" resumes the normal flow; an exception flow ends
the use case without the post-condition. Scenarios S1-S18 are in `cenarios.md`; D1-D30 in
`../decisoes_dominio.md`, D31-D50 in `decisoes_requisitos.md`.

Conventions used in all specifications:
- A **counting line** is an Item Registration of the purchase that has no Void and whose Purchase is not
  "sent to assisted checkout" (D4).
- The **total** of a purchase is the value of its counting lines after their Discounts. Card Credits are
  never deducted from it (D14). Totals and balances are derived, not stored (D2).
- Products, their current prices, the Food Product classification, stores, stations, queues, displays,
  staff, customer cards and return vouchers already exist in the system (D48).

---

## UC01 – Call next customer

**Use Case:** Call next customer
**Description:** An operator who considers themselves free signals it, and the display calls the next
customer of the single queue to that checkout. Actor: Operator.
**Scenarios:** S1
**Pre-condition:** The Operator operates an Assisted Checkout; the Store has a Queue with a Display that
leads to that Assisted Checkout.
**Post-condition:** The Display calls the next customer from the Queue to the number of that Station.

**Normal flow:**
1. Operator signals that they are free.
2. System has the Display call the next customer of the Queue to the number of the Assisted Checkout.

**Alternative flows:** none.
**Exception flows:** none. (The moment of the signal is the operator's decision; customers in the queue
are not identified, so the system does not check whether anyone is waiting, D25, D50.)

---

## UC02 – Change station mode

**Use Case:** Change station mode
**Description:** A supervisor switches one or more self-service stations between Self-Checkout Mode and
Phone-Payment Mode. Actor: Supervisor.
**Scenarios:** S15
**Pre-condition:** The Supervisor is employed by the Store, and the Self-Service Stations are Stations of
that Store.
**Post-condition:** Each Self-Service Station indicated works in the Station Mode indicated.

**Normal flow:**
1. Supervisor indicates the Self-Service Stations and the new Station Mode.
2. System checks that no Purchase in state "open" or "awaiting validation" is paid at any of those
   stations.
3. System records that each station works in the new Station Mode.
4. System confirms the change.

**Alternative flows:** none.

**Exception flows:**
(1) [a Purchase in state "open" or "awaiting validation" is paid at one of the stations] (step 2)
- 2.1. System does not change the mode of that station.
- 2.2. System informs the Supervisor which station is in use (D34).

---

## UC03 – Register assisted purchase

**Use Case:** Register assisted purchase
**Description:** The operator registers the customer's items at an assisted checkout; the customer may
identify their customer card, and pays there. Actors: Operator, Customer.
**Scenarios:** S1, S2, S3, S10, S14
**Pre-condition:** The Operator operates the Assisted Checkout.
**Post-condition:** An Assisted Purchase made in the Store, registered by the Operator and paid at the
Assisted Checkout is in state "paid", and its Payments settle its total.

**Normal flow:**
1. Operator starts a new purchase.
2. System creates an Assisted Purchase made in the Store, registered by the Operator, paid at the
   Assisted Checkout, in state "open".
3. Customer presents their Customer Card.
4. System records that the purchase is identified by the Customer Card.
5. Operator registers an item: «include» Register item.
6. Operator indicates that all items are registered.
7. «include» Pay purchase.

**Alternative flows:**
(1) [the customer has more items] (step 6)
- 6.1. Return to 5.

(2) [the customer does not present a Customer Card] (step 3)
- 3.1. System keeps the purchase without a Customer Card (D24); coupons, card credit and card balance
  are not available for it.
- 3.2. Return to 5.

(3) [the customer presents the Customer Card after some items were registered] (step 6)
- 6.1. Customer presents their Customer Card.
- 6.2. System records that the purchase is identified by the Customer Card and re-evaluates the Campaign
  Applications of all counting lines, each at its own registration time (D40).
- 6.3. Return to 6.

(4) [the customer was sent from a Validation that ended with Unexplained Discrepancy] (step 1)
- 1.1. Operator starts a new purchase for the customer sent from the validation, indicating the
  customer's Customer Card.
- 1.2. System creates an Assisted Purchase made in the Store, registered by the Operator, paid at the
  Assisted Checkout, in state "open", identified by that Customer Card, and records that the Unexplained
  Discrepancy of that card's Scan-While-Shopping Purchase sends the purchase to it (D4, D45).
- 1.3. Return to 5.

**Exception flows:**
(5) [Pay purchase ends without payment] (step 7)
- 7.1. The purchase remains in state "open" and the use case ends (the purchase can then be cancelled,
  see UC16).

---

## UC04 – Register self-checkout purchase

**Use Case:** Register self-checkout purchase
**Description:** At a self-service station working in self-checkout mode, the customer registers their
own items and pays there. Actor: Customer.
**Scenarios:** S4, S5, S13
**Pre-condition:** The Self-Service Station works in Self-Checkout Mode and no Purchase in state "open" or
"awaiting validation" is paid at it.
**Post-condition:** A Self-Checkout Purchase made in the Store and paid at the Self-Service Station is in
state "paid", and its Payments settle its total.

**Normal flow:**
1. Customer starts a purchase at the station.
2. System creates a Self-Checkout Purchase made in the Store, paid at the Self-Service Station, in state
   "open".
3. Customer presents their Customer Card.
4. System records that the purchase is identified by the Customer Card.
5. Customer registers an item: «include» Register item.
6. Customer indicates that all items are registered.
7. «include» Pay purchase.

**Alternative flows:**
(1) [the customer has more items] (step 6)
- 6.1. Return to 5.

(2) [the customer does not present a Customer Card] (step 3)
- 3.1. System keeps the purchase without a Customer Card (D24); coupons, card credit and card balance
  are not available for it.
- 3.2. Return to 5.

(3) [the customer presents the Customer Card after some items were registered] (step 6)
- 6.1. Customer presents their Customer Card.
- 6.2. System records that the purchase is identified by the Customer Card and re-evaluates the Campaign
  Applications of all counting lines, each at its own registration time (D40).
- 6.3. Return to 6.

**Exception flows:**
(4) [Pay purchase ends without payment] (step 7)
- 7.1. The purchase remains in state "open" and the use case ends (the purchase can then be cancelled,
  see UC16).

---

## UC05 – Weigh item in section

**Use Case:** Weigh item in section
**Description:** The customer weighs a product sold by weight on a section scale, which prints a label
with the code and the price computed at that moment. Actor: Customer.
**Scenarios:** S4, S6, S7
**Pre-condition:** The Section Scale belongs to the Store (D22).
**Post-condition:** A Weighing of the Product performed by the Section Scale exists, with weight, weighedAt
and price, and a Label with its code is printed for it.

**Normal flow:**
1. Customer places the product on the section scale and indicates the Product.
2. System records a Weighing of the Product performed by the Section Scale, with the weight, weighedAt =
   now and the price computed from the Product's price at that moment (D9).
3. System prints a Label with the code of that Weighing.

**Alternative flows:** none.
**Exception flows:** none.

---

## UC06 – Start scan-while-shopping purchase

**Use Case:** Start scan-while-shopping purchase
**Description:** On entering the store the customer starts, on their phone, a purchase identified by
their customer card. Actor: Customer.
**Scenarios:** S6, S17
**Pre-condition:** none.
**Post-condition:** A Scan-While-Shopping Purchase made in the Store and identified by the Customer Card
is in state "open".

**Normal flow:**
1. Customer indicates on the phone that they start a purchase in the Store and identifies their Customer
   Card.
2. System checks that the Customer Card exists and has no Scan-While-Shopping Purchase in state "open"
   or "awaiting validation".
3. System creates a Scan-While-Shopping Purchase made in the Store, identified by the Customer Card, in
   state "open".
4. System informs the customer that they can start scanning.

**Alternative flows:** none.

**Exception flows:**
(1) [the customer does not identify a valid Customer Card] (step 2)
- 2.1. System does not start the purchase.
- 2.2. System informs the customer that scanning with the phone requires a customer card (D24).

(2) [the Customer Card already has a Scan-While-Shopping Purchase in state "open" or "awaiting
validation"] (step 2)
- 2.1. System does not start the purchase.
- 2.2. System informs the customer that the card already has a purchase in progress (D41).

---

## UC07 – Scan item with phone

**Use Case:** Scan item with phone
**Description:** While going round the store, the customer scans with their phone an item taken from the
shelf (or the label of a weighed item), and it is added to their purchase at once. Actor: Customer.
**Scenarios:** S6, S7, S17
**Pre-condition:** The customer's Scan-While-Shopping Purchase is in state "open" and is not yet paid at a
Station.
**Post-condition:** The Scan-While-Shopping Purchase contains a new Item Registration, and its Campaign
Applications are up to date.

**Normal flow:**
1. Customer scans an item with the phone: «include» Register item.
2. Customer puts the item in the bag and goes on shopping.

**Alternative flows:** none (the weighed-item rule is in UC09, exception 3).
**Exception flows:** none besides those of Register item.

---

## UC08 – Finish scan-while-shopping purchase

**Use Case:** Finish scan-while-shopping purchase
**Description:** At a station working in phone-payment mode the station fetches the customer's purchase;
the purchase may be selected for validation, and the customer pays. Nothing is scanned again.
Actor: Customer. Extension point: *validation* (after step 4).
**Scenarios:** S6, S7, S8, S9, S10, S16
**Pre-condition:** The Self-Service Station works in Phone-Payment Mode and no Purchase in state "open" or
"awaiting validation" is paid at it.
**Post-condition:** The Scan-While-Shopping Purchase is paid at the Self-Service Station, is in state
"paid", and its Payments settle its total.

**Normal flow:**
1. Customer identifies their Customer Card at the station.
2. System fetches the Scan-While-Shopping Purchase in state "open" identified by that card in the Store
   and records that it is paid at this station (from now on no item can be scanned with the phone).
3. System presents the counting lines and the total.
4. System applies the Store's Validation Policy to the purchase (base percentage reduced by the card's
   past validations with No Discrepancy; forced above amountThreshold or when a flagged Product is among
   the counting lines) and the purchase is not selected (D5, D6, D46).
   *Extension point: validation.*
5. «include» Pay purchase.

**Alternative flows:** none.

**Exception flows:**
(1) [the card has no Scan-While-Shopping Purchase in state "open" in this Store] (step 2)
- 2.1. System informs the customer that there is no purchase to pay.

(2) [the purchase is in state "sent to assisted checkout"] (step 5)
- 5.1. System does not take payment.
- 5.2. System informs the customer that the items must be registered again at an assisted checkout
  (D4).

(3) [Pay purchase ends without payment] (step 5)
- 5.1. The purchase remains in state "open" and the use case ends (the purchase can then be cancelled,
  see UC16).

---

## UC09 – Register item

**Use Case:** Register item
**Description:** Behaviour shared by the three modes and by validation («include»d by UC03, UC04, UC07,
UC11): one item is registered with the price in force at that moment, and the most advantageous campaign
is applied to each line. The actor is the one of the including use case.
**Scenarios:** S1, S2, S4, S5, S6, S7, S9, S10, S12, S13, S14
**Pre-condition:** The Purchase is in state "open", or in state "awaiting validation" when the item is
added during a Validation (UC11).
**Post-condition:** The Purchase contains a new Item Registration of the Product with registeredAt and
unitPrice, and each counting line is concerned by at most one Campaign Application, the most
advantageous to the customer.

**Normal flow:**
1. Actor scans the Product.
2. System records an Item Registration of the Product in the Purchase, with registeredAt = now and
   unitPrice = the Product's price at that moment (D8, D26).
3. System re-evaluates the Campaign Applications of the counting lines: each line gets at most one, the
   most advantageous to the customer (D11), among the Campaigns covering it whose period contains the
   registration time of every line concerned (D10). Coupons issued to the Purchase's Customer Card (not
   expired, not used) are considered as Discounts that redeem them (D12, D42); Card Credit and Coupons
   are considered only if the Purchase is identified by a Customer Card (D14).
4. System presents the new line, its Campaign Application and the new total, with Card Credits shown
   apart (D14).

**Alternative flows:**
(1) [the actor scans a Label printed by a Section Scale] (step 1)
- 1.1. Actor scans the Label.
- 1.2. System records an Item Registration priced by the labelled Weighing: unitPrice = the Weighing's
  price, and campaigns for this line are evaluated at weighedAt (D9).
- 1.3. Return to 3.

(2) [the product is sold by weight, has no Label, and the Station has a Checkout Scale] (step 1)
- 1.1. Actor places the product on the Checkout Scale.
- 1.2. System records a Weighing performed by the Checkout Scale (weight, weighedAt = now, price) and an
  Item Registration priced by it (D22).
- 1.3. Return to 3.

**Exception flows:**
(3) [the product is sold by weight, has no Label, and no Checkout Scale is available (Scan-While-Shopping
Purchase, or Self-Service Station without a scale)] (step 1)
- 1.1. System does not register the item.
- 1.2. System informs the actor that the product must be weighed in the section (D30).

(4) [the Weighing of the scanned Label already prices another Item Registration] (step 1)
- 1.1. System does not register the item.
- 1.2. System informs the actor that the label was already used.

---

## UC10 – Pay purchase

**Use Case:** Pay purchase
**Description:** Behaviour shared by the three modes («include»d by UC03, UC04, UC08): the total is paid
with one or more payment instruments, applied in the fixed order return vouchers -> meal card -> card
balance -> bank card / cash (D16). The actor is the one of the including use case (at the assisted
checkout the Operator records the instruments the Customer hands over).
**Scenarios:** S1, S2, S4, S5, S6, S7, S8, S9, S10, S13, S14
**Pre-condition:** The Purchase is in state "open", is paid at a Station, and has no Payment yet.
**Post-condition:** The Purchase is in state "paid"; its Payments, numbered by sequence, settle its total;
the Coupons it redeems and the Return Vouchers it uses are used; its Card Credits count in the balance of
its Customer Card (D39).

**Normal flow:**
1. System presents the total to pay and, separately, the Card Credit the purchase earns.
2. Customer indicates the payment instruments they want to use (return vouchers, meal card, balance of
   the Customer Card, bank card or cash).
3. System splits the total among those instruments in the fixed order (D16): Voucher Payments first,
   then a Meal Card Payment of at most the value of the counting Food Product lines after their
   Discounts (D17), then a Card Balance Payment of at most the card's balance, and the rest by bank card
   or cash; it presents the split.
4. Customer pays with each instrument in that order, and the System records each Payment with its
   amount and sequence as it is accepted.
5. System records the Purchase in state "paid": the Coupons redeemed by its Discounts and the Return
   Vouchers used are no longer available, and its Card Credits are credited to the Customer Card.
6. System informs the customer of the amount paid with each instrument and of the Card Credit earned.

**Alternative flows:**
(1) [a Return Voucher is worth more than the remaining total] (step 3)
- 3.1. System uses only the part needed and informs the customer that the remainder is lost (D18).
- 3.2. Return to 4.

(2) [the meal card cannot cover what the customer wanted to pay with it, because part of the purchase is
not Food Product] (step 3)
- 3.1. System informs the customer of the maximum the meal card can pay (D17).
- 3.2. Customer indicates another instrument for the rest.
- 3.3. Return to 3.

(3) [the purchase is not identified by a Customer Card, or the card has no balance] (step 3)
- 3.1. System leaves the card balance out of the split.
- 3.2. Return to 3 for the remaining instruments.

(4) [a Return Voucher presented is already used] (step 3)
- 3.1. System does not accept that voucher and informs the customer.
- 3.2. Return to 2.

(5) [a bank card or meal card payment is refused] (step 4)
- 4.1. System informs the customer that the payment was refused.
- 4.2. Customer indicates another instrument for the amount still to pay.
- 4.3. Return to 3.

**Exception flows:**
(6) [the customer cannot or does not want to complete the payment] (step 4)
- 4.1. Customer gives up paying.
- 4.2. System keeps the Purchase in state "open" with the Payments already recorded (it can be
  cancelled, UC16, which releases vouchers, balance and coupons, D19).

---

## UC11 – Validate purchase

**Use Case:** Validate purchase
**Description:** Payment of a scan-while-shopping purchase selected by the store's rule is put on hold and
a staff member checks the items against what is registered, ending with one of three outcomes. Extends
UC08 at *validation* [purchase selected by the store's Validation Policy]. Actor: Staff Member (any of
Operator, Support Assistant, Supervisor, D27).
**Scenarios:** S8, S9, S10
**Pre-condition:** The Scan-While-Shopping Purchase is in state "open", paid at a Self-Service Station
working in Phone-Payment Mode, and selected by the Store's Validation Policy.
**Post-condition:** A Validation of the purchase, performed by the Staff Member, ends with a Validation
Outcome; the purchase is back in state "open" (No Discrepancy, Small Discrepancy) or is in state "sent to
assisted checkout" (Unexplained Discrepancy).

**Normal flow:**
1. System puts the payment on hold: the purchase goes to state "awaiting validation", a Validation of it
   is created, and the customer is told that a staff member will check the items.
2. Staff Member identifies themselves at the station.
3. System records that the Validation is performed by the Staff Member and presents the counting lines.
4. Staff Member checks the items in the bag against the counting lines and indicates that there is no
   discrepancy.
5. System records that the Validation ends with No Discrepancy and returns the purchase to state "open".

**Alternative flows:**
(1) [a small difference, explainable in good faith, is found (decided by the Staff Member, D7)] (step 4)
- 4.1. Staff Member indicates a Small Discrepancy.
- 4.2. Staff Member registers the missing item: «include» Register item (D29).
- 4.3. System records that the Validation ends with Small Discrepancy, which adds the new Item
  Registrations, and presents the recalculated total.
- 4.4. Return to 5 (the purchase returns to state "open").

(2) [a difference that cannot be explained is found (decided by the Staff Member, D7)] (step 4)
- 4.1. Staff Member indicates an Unexplained Discrepancy.
- 4.2. System records that the Validation ends with Unexplained Discrepancy and puts the purchase in state
  "sent to assisted checkout": its lines stay recorded as evidence and no longer count for payment (D4).
- 4.3. System informs the customer that the items must be registered again at an assisted checkout.

**Exception flows:** none.

---

## UC12 – Configure validation policy

**Use Case:** Configure validation policy
**Description:** Loss prevention sets, for a store, the rule that selects scan-while-shopping purchases
for validation. Actor: Loss Prevention Manager.
**Scenarios:** S11
**Pre-condition:** none.
**Post-condition:** The Store configures a Validation Policy with the basePercentage, amountThreshold and
flagged Products indicated.

**Normal flow:**
1. Loss Prevention Manager indicates the Store.
2. System presents the Store's current Validation Policy.
3. Loss Prevention Manager indicates the base percentage, the amount above which a check is forced, and
   the Products flagged as problematic.
4. System checks that the percentage is between 0 and 100 and the amount is not negative.
5. System records the Validation Policy of the Store (D6; no value is fixed by the model).

**Alternative flows:** none.

**Exception flows:**
(1) [the values indicated are not valid] (step 4)
- 4.1. System does not record the policy and informs the Loss Prevention Manager.

---

## UC13 – Define campaign

**Use Case:** Define campaign
**Description:** The campaign manager defines a campaign of one of the five kinds, with its period and the
products it covers; for a coupon campaign, personal coupons are issued. Actor: Campaign Manager.
**Scenarios:** S12
**Pre-condition:** none.
**Post-condition:** A Campaign of the indicated kind exists with start, end, covered Products and its
parameters; for a Coupon Campaign, one Coupon with the expiry date is issued to each indicated Customer
Card.

**Normal flow:**
1. Campaign Manager indicates the kind of campaign (Percentage Discount), its start and end, and the
   Products it covers.
2. Campaign Manager indicates the percentage off.
3. System checks that the data is complete and that the end is after the start.
4. System records the Percentage Discount campaign (chain-wide, D28).

**Alternative flows:**
(1) [Quantity Discount] (step 2)
- 2.1. Campaign Manager indicates the quantity taken and the quantity paid (e.g. 3 and 2).
- 2.2. Return to 3.

(2) [Cross Discount] (step 2)
- 2.1. Campaign Manager indicates the Products whose purchase is required and the discount on the
  covered Products.
- 2.2. Return to 3.

(3) [Card Credit Campaign] (step 2)
- 2.1. Campaign Manager indicates the value credited to the card.
- 2.2. Return to 3.

(4) [Coupon Campaign] (step 2)
- 2.1. Campaign Manager indicates the discount, the Customer Cards that receive a coupon and the expiry
  date.
- 2.2. System checks that the data is complete, that the end is after the start and that the cards exist.
- 2.3. System records the Coupon Campaign and issues one Coupon, with the expiry date, to each Customer
  Card indicated (D12).

**Exception flows:**
(5) [the data is incomplete or the end is not after the start] (step 3)
- 3.1. System does not record the campaign and informs the Campaign Manager.

---

## UC14 – Void item

**Use Case:** Void item
**Description:** When the customer changes their mind about an item, its line is taken off; it stays
recorded but no longer counts. Above the store's threshold a supervisor must authorise. Actors:
Operator, Customer. Extension point: *authorisation* (after step 2).
**Scenarios:** S2, S3, S6, S16
**Pre-condition:** The Purchase is in state "open" and the Item Registration is a counting line of it.
**Post-condition:** The Item Registration is cancelled by a Void (voidedAt), stays recorded, and no longer
counts for payment; the Campaign Applications of the purchase are up to date.

**Normal flow:**
1. Actor indicates the line to void.
2. System compares the line value with the Store's void threshold: it is not above it (D20).
   *Extension point: authorisation.*
3. System records a Void of the Item Registration with voidedAt = now.
4. System re-evaluates the Campaign Applications of the remaining counting lines (for example, a "buy
   three pay for two" may no longer apply) and presents the new total.

**Alternative flows:** none.

**Exception flows:**
(1) [the line value is above the threshold and the purchase is a Scan-While-Shopping Purchase not yet
paid at a Station] (step 2)
- 2.1. System does not void the line.
- 2.2. System informs the customer that this void can only be done at the payment station (D43).

(2) [the void required authorisation and it was not given] (step 3)
- 3.1. System does not record the Void; the line keeps counting.

---

## UC15 – Authorise void

**Use Case:** Authorise void
**Description:** A supervisor goes to the station and, with their card, authorises a void whose value is
above the store's threshold. Extends UC14 at *authorisation* [voided line value above the store's void
threshold]. Actor: Supervisor.
**Scenarios:** S3, S16
**Pre-condition:** The Purchase is paid at a Station (assisted checkout or self-service station).
**Post-condition:** The Void will be recorded as authorised by the Supervisor.

**Normal flow:**
1. System informs that the void needs the authorisation of a supervisor and holds it.
2. Supervisor identifies themselves with their card.
3. System checks that the person is a Supervisor employed by the Store.
4. Supervisor authorises the void.
5. System records that the Void is authorised by that Supervisor.

**Alternative flows:** none.

**Exception flows:**
(1) [the person identified is not a Supervisor of the Store] (step 3)
- 3.1. System does not accept the authorisation and informs that a supervisor is needed.

(2) [the Supervisor refuses the void] (step 4)
- 4.1. System records nothing; the void is not authorised.

---

## UC16 – Cancel purchase

**Use Case:** Cancel purchase
**Description:** A purchase that did not go through (e.g. because of a payment problem, or because the
customer gives up) is cancelled; coupons, card balance and return vouchers become available again.
Actors: Customer, Operator.
**Scenarios:** S13, S17
**Pre-condition:** The Purchase is in state "open" (D44).
**Post-condition:** The Purchase is in state "cancelled"; its lines remain recorded; the Coupons redeemed
by its Discounts and the Return Vouchers of its Voucher Payments are available again; its Card Balance
Payments no longer reduce the card's balance; its Card Credits are not credited (D13, D19, D39).

**Normal flow:**
1. Actor asks to cancel the purchase.
2. System asks for confirmation.
3. Actor confirms.
4. System records the Purchase in state "cancelled", making its Coupons, Return Vouchers and card
   balance used in it available again.
5. System informs that the purchase is cancelled and that any amount already paid by bank card or cash
   is refunded by the store's normal process, outside the system (D19).

**Alternative flows:** none.

**Exception flows:**
(1) [the actor does not confirm] (step 3)
- 3.1. The Purchase remains in state "open".

---

## UC17 – Obtain monthly void report

**Use Case:** Obtain monthly void report
**Description:** At the end of the month, loss prevention obtains, for a store, the report of the voids
that needed a supervisor's authorisation. Actor: Loss Prevention Manager.
**Scenarios:** S18
**Pre-condition:** none.
**Post-condition:** The report of the Voids authorised by a Supervisor in the Store's purchases in that
month is presented (nothing in the system changes).

**Normal flow:**
1. Loss Prevention Manager indicates the Store and the month.
2. System selects the Voids with voidedAt in that month, of Item Registrations of Purchases made in the
   Store, that are authorised by a Supervisor (D20, D36).
3. System presents, for each, the date, the Product and value of the voided line, the kind of purchase
   and the Supervisor, with the number and total value of the voids.

**Alternative flows:**
(1) [there are no authorised voids in that month] (step 2)
- 2.1. System informs that there are no authorised voids in that month.

**Exception flows:** none.
