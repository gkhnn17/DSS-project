# Subsystems and interfaces

Diagram: `architecture.puml` (components) and `extension_points.puml` (classes behind the interfaces).
Decisions: `design_decisions.md` (D56-D74). Types in `code` are design-level names, not final Java code.

## Layers (D74)

```
UI (Customer phone app, Payment station, Staff app)
        │ uses
ISupermarketLN  (business-layer facade: one operation per use-case step)
        │ delegates to
IPurchases · ICatalog · ICampaigns · IPayments · IVerification · ICustomerCards
        │ each with its own DAOs                     IPayments ──► IBankGateway (external)
```

## ISupermarketLN (facade used by the UI)

| Operation | Use case | Delegates to |
|---|---|---|
| `startPurchase(cardId, storeId) : PurchaseId` | Register item (first scan) | ICustomerCards, IPurchases |
| `registerItem(purchaseId, labelCode) : PurchaseSummary` | Register item; missing items (D70) | ICatalog, IPurchases, ICampaigns |
| `cancelLine(purchaseId, lineId) : PurchaseSummary` | Register item, correction (D63) | IPurchases, ICampaigns |
| `startPayment(purchaseId) : PaymentStart` | Pay for purchase | IPayments → IVerification |
| `pay(purchaseId, balanceAmount, bankCardData) : Receipt` | Pay for purchase (D68) | IPayments |
| `pendingVerifications(storeId) : List<PurchaseId>` | Verify purchase | IVerification |
| `registeredItems(purchaseId) : List<ItemLine>` | Verify purchase | IPurchases |
| `markCompliant(purchaseId, staffId)` | Verify purchase | IVerification |
| `markNotAcceptable(purchaseId, staffId)` | Verify purchase | IVerification |

`PurchaseSummary` = lines (with cancelled ones marked), effects, total. `PaymentStart` = `UnderVerification` or
`ReadyToPay(total, availableBalance)`.

## IPurchases (Purchases subsystem)

Owns `ScanPurchase`, `ItemLine` and the purchase state (`stm_purchase.puml`).

| Operation | Notes |
|---|---|
| `startPurchase(cardId, storeId) : PurchaseId` | Refused if the card already has an open purchase (D73). |
| `addLine(purchaseId, LabelReading, at) : LineId` | Allowed in `Open` and `UnderVerification` (D70). |
| `cancelLine(purchaseId, lineId, at)` | Only in `Open` (D63); the line is kept with `cancelledAt`. |
| `view(purchaseId) : PurchaseView` | Read-only view used by campaigns, payment and verification. |
| `setEffects(purchaseId, List<CampaignEffect>)` | Result of the latest campaign evaluation (D66). |
| `changeState(purchaseId, PurchaseState)` | Checks the allowed transitions. |
| `countPaidPurchases(cardId) : int` | Input of the verification strategy (D60). |

## ICatalog (Catalog subsystem)

| Operation | Notes |
|---|---|
| `readLabel(labelCode) : LabelReading` | Product, unit price, optional weight. Product code → current price; section-scale label → weight and price at weighing time (D64). Error if the code is unknown. |

## ICampaigns (Campaigns subsystem): extension point 1

| Operation | Notes |
|---|---|
| `evaluate(PurchaseView) : List<CampaignEffect>` | Asks each campaign valid for at least one line; keeps at most one effect per line, the most advantageous (D11, D66). |

Inside: `Campaign` (abstract: `start`, `end`, `isValidAt(t)`, `evaluate(PurchaseView)`), implemented by
`BuyTwoPayOne` (D65). `CampaignEffect` (`discount()`, `onPaid(card)`), implemented by `Discount` (D67).
New campaign type = new subclass of `Campaign` (+ a new `CampaignEffect` only if it is not a discount).

## IPayments (Payments subsystem): extension point 2

| Operation | Notes |
|---|---|
| `startPayment(purchaseId) : PaymentStart` | Freezes the purchase (D72) and asks `IVerification.decide`. |
| `pay(purchaseId, List<PaymentRequest>) : Receipt` | Checks Σ amounts = total and each amount ≤ `maxAmount`; charges each instrument; on failure releases the ones already charged (D68). On success: state `Paid`, `onPaid` of each effect (D67). |

Inside: `PaymentInstrument` (`maxAmount(view)`, `charge(amount)`, `release(amount)`), implemented by
`CardBalanceInstrument` (uses `ICustomerCards`) and `BankCardInstrument` (uses `IBankGateway`). `PaymentPart`
records instrument and amount. New instrument = new class implementing `PaymentInstrument` (D69).

## IVerification (Verification subsystem)

| Operation | Notes |
|---|---|
| `decide(purchaseId) : boolean` | Uses the store's `VerificationStrategy` (D61, D62); if true, creates a `Verification` and sets the state `UnderVerification`. |
| `pendingVerifications(storeId) : List<PurchaseId>` | Purchases waiting for a staff member. |
| `markCompliant(purchaseId, staffId)` | Records the outcome (D71); state → `ReadyToPay`. |
| `markNotAcceptable(purchaseId, staffId)` | Records the outcome; state → `NotAcceptable` (final, D59). |

Inside: `VerificationStrategy` (`mustVerify(paidPurchases) : boolean`), implemented by
`DecreasingProbabilityStrategy(pBase, f, pMin, random)`.

## ICustomerCards (CustomerCards subsystem)

| Operation | Notes |
|---|---|
| `exists(cardId) : boolean` | Used by `startPurchase` (D57). |
| `balance(cardId) : Money` | Available balance. |
| `debit(cardId, amount)` / `credit(cardId, amount)` | `debit` by the balance instrument; `credit` to release it and, later, by card-credit campaigns. |

## IBankGateway (external, required by Payments)

| Operation | Notes |
|---|---|
| `charge(bankCardData, amount) : Authorisation` | May refuse; refusal is an alternative flow of Pay for purchase. |
