# BRD — Unified Payment Gateway (PayRoute)

| Project | PayRoute Gateway v2 |
|---------|---------------------|

---

## 1. Business Objectives

- One integration contract for POS, eCom, and mobile.
- Eliminate duplicate charges from retries and timeouts.
- Merchant self-service for refunds and transaction search.

## 2. Payment State Machine (BR-PAY-01)

Allowed transitions:

| From | Event | To |
|------|-------|-----|
| — | `authorize` | AUTHORIZED |
| AUTHORIZED | `capture` (full/partial) | CAPTURED |
| AUTHORIZED | `void` | VOIDED |
| AUTHORIZED | `expire` (after 7 days) | EXPIRED |
| CAPTURED | `refund` (full/partial) | REFUNDED / PARTIALLY_REFUNDED |
| CAPTURED | clearing | SETTLED |

Invalid transitions SHALL return `409 INVALID_STATE`.

## 3. Idempotency (BR-PAY-02)

- Client MUST send `Idempotency-Key` header on POST `/payments`.
- Same key + same payload within 24h → return original response (HTTP 200).
- Same key + different payload → HTTP 422 `IDEMPOTENCY_CONFLICT`.

## 4. Offline POS (BR-PAY-03)

| Rule | Detail |
|------|--------|
| Floor limit | €50 per offline txn (merchant risk tier configurable) |
| Sync window | Terminal MUST sync within 24h |
| Decline on sync fail | If auth declined at sync, merchant notified for collection |

## 5. Functional Requirements

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-01 | REST API: authorize, capture, void, refund | Must |
| FR-02 | Idempotency per BR-PAY-02 | Must |
| FR-03 | Webhook events for state changes | Must |
| FR-04 | Merchant portal — txn search, export, refund | Must |
| FR-05 | Partial capture up to 115% of auth (MCC configurable) | Should |
| FR-06 | Terminal SDK — offline queue with sync status | Must |
| FR-07 | Unified `paymentId` across channels | Must |
| FR-08 | PCI — SAQ A for eCom (hosted fields); P2PE for POS | Must |

## 6. Merchant Onboarding (BR-MER-01)

| Step | Output |
|------|--------|
| KYC/KYB complete | Merchant legal entity ID |
| Risk tier assigned | Floor limits, settlement delay |
| MID/TID provisioned | Acquirer registration |
| Terminal shipped / SDK keys | Activation |

High-risk MCCs (e.g. crypto, travel) → delayed settlement T+3.

## 7. NFRs

| ID | Requirement |
|----|-------------|
| NFR-01 | Auth p95 < 800ms EU domestic |
| NFR-02 | 99.95% availability |
| NFR-03 | Webhook delivery retry 72h exponential backoff |
| NFR-04 | Transaction search < 2s for 90-day window |

## 8. Reporting

- Settlement report by MID, daily
- Auth vs capture mismatch report
- Offline backlog aging
