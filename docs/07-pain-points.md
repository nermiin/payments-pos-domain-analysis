# Pain Points — Payments & POS (Prioritized)

| Rank | ID | Pain Point | Impact | Proposed Solution |
|------|-----|------------|--------|-------------------|
| 1 | PP-P01 | Duplicate charges on timeout retry | Chargebacks, reputational | Idempotency keys (BR-PAY-02) |
| 2 | PP-P02 | Split POS/eCom stacks | 2× integration cost | Unified gateway (FR-07) |
| 3 | PP-P03 | Offline txn invisible to merchant | Revenue leakage, support load | Offline dashboard (US-PAY-03) |
| 4 | PP-P04 | No partial capture | Lost verticals (hotels, rentals) | FR-05 |
| 5 | PP-P05 | Refunds via acquirer only | Slow merchant service | US-PAY-04 |
| 6 | PP-P06 | Generic decline messages | Poor checkout UX | Issuer response code mapping |
| 7 | PP-P07 | Settlement reporting delayed | Cash flow planning | Daily settlement API |

## MoSCoW Summary

| Must | Should | Could |
|------|--------|-------|
| Idempotency, unified paymentId, webhooks | Partial capture, MCC-based settlement delay | Multi-currency DCC |

## Gap Analysis — Regulatory

| Requirement | As-Is | To-Be |
|-------------|-------|-------|
| PSD2 SCA for eCom | Partial — some merchants exempt incorrectly | Gateway enforces 3DS per txn risk |
| PCI SAQ scope | Mixed integrations | Hosted fields + P2PE terminals |
