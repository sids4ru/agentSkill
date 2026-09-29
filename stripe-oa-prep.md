---
name: stripe-oa-prep
description: Practical coding assessment coach for fintech and infrastructure online assessments (e.g. Stripe, Square, Adyen, Brex). Use when practicing complex state engines, multi-part coding problems, ledger math, rate limiters, or billing and fraud engines.
---

# Stripe OA & Practical Coding Assessment Coach

A specialized guide and interview engine for high-difficulty, multi-part practical software engineering assessments focusing on real-world fintech systems and stateful engines.

## Core Problem Archetypes

1. **Idempotency & Replay Engine**
   - Compound tenant keys (`merchant_id:idempotency_key`).
   - Rolling TTL expirations (e.g., 120s sliding window).
   - Conflict detection on differing payload bodies vs. cached replay of identical requests.

2. **Atlas Business Name / Entity Normalizer**
   - Multi-pass regex normalization and delimiter stripping.
   - Stop-word / leading article removal (`the`, `a`, `an`).
   - Corporate legal suffix truncation (`inc`, `llc`, `corp`, `ltd`).
   - Conflict attribution mapping.

3. **Wire Reconciliation & Ledger Engine**
   - Ledger waterfalls and FIFO balance matching.
   - Targeted memo matching (`memo: <inv_id>`).
   - Partial payment handling and remainder tracking.

4. **Radar Fraud & Velocity Engine**
   - Rolling sliding time-window aggregations ([T - W, T] or [T - W, T)).
   - Differentiating states: counting only `APPROVED` for velocity vs. `ALL` attempts for merchant hopping.
   - Geographic anomaly detection ("impossible travel" across time deltas).

5. **Multi-Currency FX Settlement**
   - Multi-tier liquidity waterfalls across pegged currencies.
   - Integer rounding on conversions (`Math.floor` / `Math.ceil`).
   - Platform fee gross-up calculations (e.g., 2% flat conversion fee).
   - Atomic evaluation: staging mutations on a working copy before committing.

6. **Subscription Proration & Billing**
   - Timeline partitioning (e.g., 720-hour cycle).
   - Integer cent proration arithmetic: `Math.floor(((total_units - elapsed) * price) / total_units)`.
   - Mid-cycle starts, tier adjustments, and status tracking (`ACTIVE` vs. `CANCELLED`).

7. **API Rate Limiter / Token Bucket**
   - Sliding window per API key and route.
   - Tiered limits (`STANDARD` vs. `ENTERPRISE`).
   - Rejection semantics: blocked requests do not consume window quota.

## Universal Implementation Principles

- **No Premature State Mutation:** Always clone state (`const temp = { ...state }`) before evaluating waterfall checks or rule sets. Commit only when the full cascade succeeds.
- **Strict Integer Financial Math:** Never perform floating-point math directly on cents. Multiply before dividing, and use explicit rounding functions (`Math.floor` / `Math.ceil`).
- **Exact Interval Boundaries:** Verify interval definitions strictly (T - Δt <= t <= T vs T - Δt < t < T) to avoid boundary drift.
- **Deterministic Output:** Sort keys explicitly (`Object.keys(...).sort()`) and format strings with exact delimiters. Never emit stray debug logs.
