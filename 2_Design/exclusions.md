# What was left out of the design model, and why

Base sentence of the scope restriction: "The design and implementation phases address exclusively the scan-as-you-shop
mode (purchase using a mobile phone). [...] All other functionalities are outside the scope of these phases."
Phase 1 elements are kept in `../1_Requirements/` as the analysis of the whole system; they are not deleted.

## Excluded by the scope restriction

| Element (Phase 1 reference) | Why it is out |
|---|---|
| Assisted checkout, operator, queues and the "free" signal (D21, D25, D45, D50) | Not the scan-as-you-shop mode. |
| Self-checkout, station modes and changing a station's mode (D21, D34) | Not the scan-as-you-shop mode. The payment station is only where the customer pays. |
| Checkout scale; weighing at the station (D22) | Weighed items in this mode come with a section-scale label (D30, D64). |
| Weighing at the section scale as a use case (D47) | The scope restriction assumes "all items are labelled"; the label is an input. |
| Voids with supervisor authorisation, void threshold, monthly void report (D20, D36, D43, D53) | Not one of the three functionalities. Removal of a line by the customer is kept in a simpler form (D63). |
| Re-registration at an assisted checkout after an unexplained discrepancy (D4, D45) | "what happens next is outside the scope" (D59). |
| Forced checks above an amount, problematic products, incident history; configuring the policy (D6, D33, D55) | Only the probabilistic rule decreasing with the number of purchases is required. Shown as a future strategy (D62). |
| Percentage, quantity, coupon, cross-discount and card-credit campaigns; coupons (D10-D14, D42, D54) | Only "buy 2, pay 1" is required. They fit as new `Campaign` classes (D66, D67, `extension_points.puml`). |
| Cash, meal card, return voucher (D16-D18) | Only card balance and bank card are required. They fit as new `PaymentInstrument` classes (D69). |
| Crediting the card balance (D14, D39) | No functionality earns balance in this phase; it is assumed to exist (D67). |
| Cancelling a purchase and restoring coupon, balance or voucher (D19, D44) | Not one of the three functionalities (D73). |
| Defining campaigns, products and prices; issuing cards (D28, D32, D48) | Data assumed to exist, as in D48. |
| Anonymous purchases (D24, 0..1 card in other modes) | Every purchase is associated with a card (D57). |
| Family / multi-user cards (D23) | Unchanged: not supported. |

## Open forum questions that become out of scope (no teacher answer)

| Forum question | Why it no longer matters for the design |
|---|---|
| Is the monthly void report automatic or on request? (Alberto) | The report is excluded. |
| Can the scale of a payment station be used in payment mode? (Alberto) | Station scales are excluded; weighed items carry a label (D64). |
| Price of items scanned again at an assisted checkout (Marisa) | Assisted re-registration is excluded (D59). |
| Card-credit campaigns in this phase (08/11) | Answered by our decision D67: extension point only. |
| Removing a wrongly scanned item (07/11) | Answered by our decision D63. |
| Label class linked to Product (28/10) | Answered by our decision D64. |

## Left out of the model on purpose (design level)

- **Phone app screens and the payment-station screen**: the UI layer is shown as one component; screens are
  implementation detail.
- **The bank and the card terminal**: external; represented only by the `IBankGateway` interface.
- **Persistence technology**: each subsystem has DAOs; the database choice is left to the implementation phase.
- **Receipt**: derived from lines, effects and payment parts (D2).
