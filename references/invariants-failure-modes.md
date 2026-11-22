# Invariants & failure modes

## Prioritization

- **P0 — Money & core transactions**: payments, commissions, escrow, payouts, refunds, disputes, invoicing, any transaction that changes value or data integrity.
- **P1 — Integrity & sync**: cross-authentication, status synchronization, data consistency, critical webhooks/events, especially across systems or modules.
- **P2 — Secondary**: search, display, non-critical admin features.

Only P0 domains require the full audit below. P1 gets the invariants table and skips failure modes; P2 gets states and rules only. A domain that holds a bridge between stacks or stores audits I4 whatever its priority (see `multi-stack.md`).

## Invariants (P0 mandatory)

For each P0 flow, give a verdict with the protecting mechanism (constraint, lock, idempotency key, check) and its evidence:

| Verdict | Meaning |
|---|---|
| `PROTECTED` | A mechanism read in code enforces the invariant on every path that writes. |
| `PARTIAL` | A mechanism exists but leaves a path open (e.g. sequential retries covered, concurrent ones not). Say which path. |
| `ABSENT` | The writing paths were read and no mechanism protects the invariant. |
| `UNKNOWN` | The writing paths, or the mechanism, were not read. Becomes a gray area with a question; never use it for "read and found nothing" (that is `ABSENT`). |

These verdicts qualify an invariant; they are distinct from domain statuses (`COVERED`, `PARTIAL`…) defined in SKILL.md.

| # | Invariant |
|---|---|
| I1 | No duplicate execution or double processing of the same event / request. |
| I2 | No negative or zero amount where forbidden. |
| I3 | No business record without its audit / transaction log, and no log without its record. |
| I4 | No durable divergence between decoupled systems or databases without a reconciliation mechanism. |
| I5 | No orphaned relation across tables or services that lack foreign-key constraints and rely on application code only. |

## Failure modes (P0 mandatory)

For each P0 flow, answer each point with what the code does, not what it should do:

1. **Concurrency & double call** — locks (pessimistic, distributed), unique constraints, idempotency tokens. What happens on a double click, a retried HTTP call, a replayed webhook?
2. **Timeouts & orphan states** — behavior when an external dependency is slow or down; asynchronous events arriving out of order; entities stuck in an intermediate status.
3. **Partial failure & async resilience** — primary entity created but a secondary step fails: rollback, compensation, or inconsistency? Where do failed jobs/events land (retry queue, DLQ, log only, nowhere)?
4. **Configuration & hidden flags** — environment variables, feature flags, DB configuration or constants that change the core logic; their default values and where they are set.
5. **Chaos scenario** (especially for money flows): Logical impact if a distributed lock (Redis, etc.) or cache expires prematurely during the synchronous processing of a critical webhook or transactional step.
