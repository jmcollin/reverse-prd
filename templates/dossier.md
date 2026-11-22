# Reverse-PRD — {{system}}

_Date: {{YYYY-MM-DD}} · Repos: {{paths}} · Architecture: {{single-stack | multi-stack}} · Plan: [phase0.md](phase0.md)_

## 0. Executive summary & risk matrix

**Summary** (6–10 lines): how the system really works, whether it is multi-stack, and the 3–5 major risks.

| Domain | Risk | Confidence | Impact | Impact if broken | Recommended action |
|---|---|---|---|---|---|

- **Impact**: consequence observed or likely today, with the current protections.
- **Impact if broken**: worst case if the protecting mechanism fails (lock, idempotency key, reconciliation, constraint) — amount at stake, data affected, users affected.

**Coverage**
| Domain | Priority | Status | File |
|---|---|---|---|

**Out of scope**:

## 1. System boundaries & interoperability
- Contact points, protocols, data stores (diagram).
- Real permissions matrix: what each role can do in the UI vs through the API/back-end.
- Key data mapping for P0: source vs target tables/entities.
- _(Multi-stack)_ bridge map between stacks: shared tables, sessions, queues, hooks, sync scripts, and the owner of each shared entity.

## 2. Actors & objectives
| Actor (user, admin, external system, worker) | Business objective | Effective roles & permissions |
|---|---|---|

## 3. Business model & state machines
Per critical entity: one line + link to the domain file holding the diagram.

## 4. Critical flows (P0)
Per flow: one line, invariant verdicts summary, link to the domain file.

## 5. Business rules repository
Merged from all domain files, sorted by domain then risk.

| ID | Domain | Exact rule | Business formulation | Dependencies | Source of truth | Code evidence | Integration evidence | Confidence & risk |
|---|---|---|---|---|---|---|---|---|

## 6. Automations & reconciliation
- Inventory: crons, workers, queues, webhooks — trigger, retry policy, where failures land (retry queue, DLQ, log only, nowhere).
- Reconciliation mechanisms per bridge or external system, and gaps.

## 7. Technical debt, legacy pitfalls & gray areas
- Dead vs active code, hardcoded values, hidden configuration, integrity risks without constraints.
- `[CODE / UI CONFLICT DETECTED]` items.
- _(Multi-stack)_ cross-system consistency risks.
- Open questions: one per gray area, blocking ones first.
