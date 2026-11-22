# {{nn}} — {{Domain}} ({{P0|P1|P2}})

_Snapshot: {{repo @ commit SHA, or "worktree"}} · Budget tier: {{S | M | L}} · Pass: {{1 | 2 (continuation)}}_

## Summary
3–5 lines: what this domain does for the business, and the main risk found.

## Entities & state machines
For each critical entity:
- **Source of truth**: table/column, service, or computation.
- **Data mapping** (P0): where the value is written vs where it is read (source vs target table or entity), when they differ.
- **States**: exact stored values, how they are exposed (see Glossary terms), and their meaning.
- **Transitions**: trigger (user action, cron, webhook, event), guard, side effects, evidence.

```mermaid
stateDiagram-v2
```

_No state machine (e.g. a pure calculation)? Write "No state machine" and drop the block; the sequence diagram below stays required._

## Actors & permissions
Who acts in this domain (user role, admin, external system, worker), what they can do, and **what enforces it** — the guard, middleware or check, with evidence. An annotation or a documented scope is not enforcement.

| Actor | Can do | Enforced by | Evidence |
|---|---|---|---|

## Critical flows
For each flow:
- Steps with back-end calls and data written, evidence per step. For P0 rules, give the 5–15 line extracts here.
- Error messages observed or found in code, verbatim.
- **Touchpoints**: screens / endpoints exposed, eligibility conditions visible to the user, business delays (only from a constant, cron or config — otherwise gray area).
- **Notifications triggered**: emails, client webhooks, UI messages — trigger, recipient role, template name.

```mermaid
sequenceDiagram
```

**User story**: As a … I want … so that …

```gherkin
Scenario: happy path
  Given …
  When …
  Then …

Scenario: critical failure
  Given …
  When …
  Then …
```

## Business rules
Rows to merge into the dossier table (IDs prefixed with the domain number, e.g. `R{{nn}}.1`). Business formulation first: a product manager must be able to validate it without reading code. The evidence cells hold tags only (`[Code: …]`, `[Integration: …]`); extracts go in Critical flows.

| ID | Business formulation | Exact rule (thresholds, formulas, delays, values) | Dependencies | Source of truth | Code evidence | Integration evidence | Confidence & risk |
|---|---|---|---|---|---|---|---|

## Glossary terms
2–5 key business terms of this domain, merged into dossier section 1.

**Exposed as**, from the interfaces in scope (Phase 0): the screen label (`UI`); the value an API client or webhook receiver gets, e.g. `status: "paid"` or event `payout.failed` (`API only`); `label / API value` (`both`); `n/a` for an internal term or when the scope is `none`. The column is always kept.

| Business term | Exposed as | Name in code / data | Definition (one sentence) |
|---|---|---|---|

## Invariants (P0/P1)
Verdicts `PROTECTED` / `PARTIAL` / `ABSENT` / `UNKNOWN`, defined in `references/invariants-failure-modes.md`. P2 domains skip this section; P1 domains skip only the next one (except I4 for a bridge, which every domain audits).

| Invariant | Verdict | Mechanism | Evidence |
|---|---|---|---|
| I1 Double processing | | | |
| I2 Forbidden negative/zero | | | |
| I3 Record ↔ audit log | | | |
| I4 Cross-system divergence | | | |
| I5 Orphans without FK | | | |

## Failure modes (P0)
1. Concurrency & double call:
2. Timeouts & orphan states:
3. Partial failure & async resilience:
4. Configuration & hidden flags:
5. Chaos scenario (lock / cache expiry):

## Operations
### Automations & reconciliation
- Crons, workers, queues, webhooks touching this domain: schedule/trigger, retry policy, where failures land (retry queue, DLQ, log only, nowhere).
- Reconciliation mechanisms with other systems, and gaps.
- _(Multi-stack)_ bridges this domain uses (shared tables, queues, hooks, sync scripts) and the owner of each shared entity.

### Observability & recovery
Feeds dossier section 8 (runbook).
- What is logged / tracked / alerted when a critical flow fails — or nothing.
- **Silent failures**: swallowed errors on the money path, with `[Code: …]`.
- **Manual recovery**: back-office actions, console commands, replay/retry endpoints that unblock a stuck state; who can run them. None found → say so.
- **Degraded mode**: behavior when an external dependency is down.

## Technical debt & gray areas
Dead vs active code, hardcoded values, hidden flags, conflicts. One precise question per gray area, each with `Owner:` (who can answer it) and whether it **blocks** the domain.

## Layer separation (only when a UI is in scope)
Feeds dossier section 4. Omit this section for `API only` or `none`.

| Aspect | Backend | Frontend |
|--------|---------|----------|
| Source of truth | | |
| Key entry points | | |
| Critical rules / validations | | |
| Failure modes specific to this layer | | |

### Backend vs Frontend boundaries
- What lives only in the backend
- What is duplicated or validated on both sides
- What is purely frontend (with no backend guarantee)

---

## Exit contract
- **Status**: `[DOMAIN STATUS: COVERED | COVERED (code only) | PARTIAL | BLOCKED]` — one line why (definitions in SKILL.md). Proposed by the auditor, set by the orchestrator, who also writes it here.
- **Budget used**: `tier <S|M|L> · <n>/<cap> exploration calls · <n>/<cap> files in depth` — one line per pass.
- **Key code evidence** (1–2): `[Code: <file>:<L1>-<L2>] — what it proves`.
- **Integration**: observation made, or the checks to run in the format of `references/integration.md`, marking side effects.
- **Diagrams**: count and type (at least 1 sequence diagram).
- **Blocking questions**: numbered, each with its owner; possibly none.
- **Top risks**: up to 3, one line each with impact.
- **Continuation** (PARTIAL): up to 5 unexplored leads, highest risk first — `symbol or file · question it would answer · priority step it belongs to`. Cross-boundary leads name the owning domain.
