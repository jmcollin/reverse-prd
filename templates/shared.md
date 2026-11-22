# Shared infrastructure — {{system}}

_Snapshot: {{repo @ commit SHA, one per repo}} · Audited once before the domains (orchestrator budget). Every brief receives these facts as **Known facts**._

Each fact has an ID that risk rows and domain files cite (`S1`, `S2`…). Facts added later — proven by a domain and re-read by the orchestrator — keep the next free ID and record their origin.

| ID | Fact | Evidence | Confidence | Origin | Consequence for domains |
|---|---|---|---|---|---|
| S1 | | `[Code: <file>:<L1>-<L2>]` | `[Inferred from code (untested)]` | shared pass · or `from domain <nn>, verified at consolidation` · or `stale` (commit changed, not re-verified) | |

Cover, for each shared component listed in Phase 0:
- **Write path end to end**: what commits an aggregate or entity, which publisher or bus writes it, and whether it happens before or after external calls.
- **Write concurrency**: expected version, locks, unique constraints, transactions.
- **Retries and failure landing**: retry and backoff options, dead-letter or failed-job handling.
- **Idempotency helpers** and their use by external clients (payment provider, HTTP clients).
- **Alerting**: can an alert throw, be dropped, or be lost on a crash?
- **Access guard**: what is actually checked, as opposed to what annotations declare.

**Not audited (budget)**: components or questions left open, which domains must cite as gray areas.

**Budget used**: {{n}} calls.
