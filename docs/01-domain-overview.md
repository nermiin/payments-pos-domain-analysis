# Domain Overview — Payments & POS

## 1. Payment Transaction Lifecycle

```mermaid
stateDiagram-v2
    [*] --> Authorized: Auth request approved
    Authorized --> Captured: Merchant capture/settlement batch
    Authorized --> Reversed: Auth reversal (full/partial)
    Authorized --> Expired: Auth hold released (no capture)
    Captured --> Settled: Funds movement T+1/T+2
    Captured --> Refunded: Customer refund
    Settled --> [*]
```

## 2. Ecosystem Actors

| Actor | Responsibility |
|-------|----------------|
| **Cardholder** | Pays with physical or virtual card |
| **Merchant** | Accepts payment for goods/services |
| **POS / Payment Gateway** | Captures card data, routes to acquirer |
| **Acquirer** | Merchant relationship, clearing to scheme |
| **Scheme** | Visa, Mastercard — rules and network |
| **Issuer** | Cardholder account, authorization decision |
| **Processor** | Technical auth/clearing switch |

## 3. Card-Present vs Card-Not-Present

| Dimension | Card-Present (POS) | Card-Not-Present (eCom) |
|-----------|-------------------|-------------------------|
| Entry mode | Chip, contactless, swipe | PAN entry, tokenized wallet |
| Liability | EMV shift if chip used | 3DS, AVS, CVV rules |
| Failure modes | Terminal offline store-and-forward | Timeout, duplicate submit |
| Settlement | Often batch per terminal EOD | Real-time or batch capture |

## 4. Message Flow (Simplified)

```mermaid
sequenceDiagram
    participant POS as POS Terminal
    participant GW as Payment Gateway
    participant ACQ as Acquirer
    participant SCH as Scheme
    participant ISS as Issuer

    POS->>GW: Sale €45.00
    GW->>ACQ: Auth request
    ACQ->>SCH: ISO 8583 / API
    SCH->>ISS: Auth request
    ISS-->>SCH: Approval + auth code
    SCH-->>ACQ: Response
    ACQ-->>GW: 00 Approved
    GW-->>POS: Print receipt
    Note over ACQ,SCH: Clearing file EOD
    Note over ACQ: Settlement T+1 to merchant
```

## 5. Initiative Scope — Unified Payment Gateway

Fictional merchant platform **PayRoute** consolidates POS, eCom, and mobile SDK into one gateway with consistent idempotency, reporting, and failure handling.

## 6. Success Criteria

- Auth p95 latency < 800ms domestic
- Duplicate charge rate < 0.01%
- Terminal offline transactions reconciled within 24h
- Merchant-visible status for auth → capture → settlement
