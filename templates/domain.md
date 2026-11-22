# {{nn}} — {{Domain}} ({{P0|P1|P2}})

## Summary
3–5 lines: what this domain does for the business, and the main risk found.

## Entities & state machines
For each critical entity:
- **Source of truth**: table/column, service, or computation.
- **States**: exact stored values and their meaning.
- **Transitions**: trigger (user action, cron, webhook, event), guard, side effects, evidence.

```mermaid
stateDiagram-v2
```

## Critical flows
For each flow:
- Steps with back-end calls and data written, evidence per step.
- Error messages observed or found in code, verbatim.

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
Rows to merge into the dossier table (IDs prefixed with the domain number, e.g. `R{{nn}}.1`).

| ID | Exact rule (thresholds, formulas, delays, values) | Business formulation | Dependencies | Source of truth | Code evidence | Integration evidence | Confidence & risk |
|---|---|---|---|---|---|---|---|

## Invariants (P0/P1)
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

## Automations
Crons, workers, queues, webhooks touching this domain: schedule/trigger, retry policy, where failures land.

## Technical debt & gray areas
Dead vs active code, hardcoded values, hidden flags, conflicts. One precise question per gray area.

---

## Exit contract
- **Status**: `[DOMAIN STATUS: COVERED | PARTIAL | BLOCKED]` — one line why.
- **Key code evidence** (1–2): `file:line — what it proves`.
- **Integration**: observation made, or the checks to run (`environment · role · steps · expected value`).
- **Diagrams**: count and type (at least 1).
- **Blocking questions**: numbered, possibly none.
- **Top risks**: up to 3, one line each with impact.
