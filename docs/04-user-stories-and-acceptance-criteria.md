# User Stories — Payments & POS

### US-PAY-01 — Authorize with idempotency

**As a** merchant integration developer  
**I want** idempotent payment creation  
**So that** network retries do not double-charge customers

```gherkin
  Scenario: Retry with same idempotency key
    Given a successful authorization with Idempotency-Key "key-abc"
    When the client retries POST /payments with the same key and body
    Then response is HTTP 200 with the original paymentId
    And only one authorization exists at the acquirer
```

---

### US-PAY-02 — Partial capture (hotels)

**As a** hotel merchant  
**I want** to capture less than the authorized deposit  
**So that** I only charge the final bill amount

- Auth €200, capture €183.50 → remaining auth released automatically.

---

### US-PAY-03 — Offline terminal sync

**As a** store manager  
**I want** visibility into offline transactions pending sync  
**So that** I can ensure terminals reconnect before the 24h deadline

---

### US-PAY-04 — Merchant refund

**As a** merchant support user  
**I want** to issue full or partial refunds from the portal  
**So that** I do not need acquirer portal access

- Refund within 180 days of capture.
- Balance check: refund amount ≤ captured − prior refunds.

---

### US-PAY-05 — Payment webhooks

**As a** merchant backend  
**I want** webhooks on `payment.captured` and `payment.refunded`  
**So that** I can update order status in real time

---

### US-PAY-06 — Timeout handling

**As a** customer  
**I want** clear outcome when payment times out  
**So that** I know whether to retry

- Gateway returns `UNKNOWN` with `advice: query_status` — client polls GET `/payments/{id}`.
