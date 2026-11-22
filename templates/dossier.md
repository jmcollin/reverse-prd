# Reverse-PRD — {{system}}

_Date: {{YYYY-MM-DD}} · Architecture: {{single-stack | multi-stack}} · Interfaces: {{UI | API only | both | none}} · Plan: [phase0.md](phase0.md) · Shared infrastructure: [shared.md](shared.md) (if any)_

_Every section below holds `_pending_` until Step 6 fills it._
_Snapshot: {{repo @ commit SHA, one per repo}} · Integration env: {{name · deployed version or "unknown"}}_

> **How to read this dossier**
> - **Part I (sections 0–3)** is for product: no code needed. **Part II (4–6)** is for engineering. **Part III (7–9)** is for operations and support.
> - `[Verified UI/Code]` — read in the code **and** seen working on a test environment (UI or API). Safe to rely on.
> - `[Inferred from code (untested)]` — read in the code, never observed. Likely true; confirm before a decision that costs money.
> - `[Gray area / To validate]` — not proven. Each one comes with a question in section 9.
> - `[CODE / OBSERVATION CONFLICT DETECTED]` — code and observed behavior disagree; the observed behavior is described as the product.
> - IDs: `R<nn>.x` = business rule of domain `<nn>`; `I1`–`I5` = integrity invariants (double processing, forbidden amounts, audit log, cross-system divergence, orphans); `S<n>` = shared-infrastructure fact in `shared.md`.

---

# Part I — Product

## 0. Executive summary & risk matrix

**Summary** (6–10 lines): how the system really works, whether it is multi-stack, and the 3–5 major risks in business terms (money, users, data).

| Domain | Risk | Rule / invariant / shared IDs | Confidence | Impact | Impact if broken | Recommended action |
|---|---|---|---|---|---|---|

- **Confidence**: one of the tags above.
- **Impact**: consequence observed or likely today, with the current protections — amount, users or revenue affected when known.
- **Impact if broken**: worst case if the protecting mechanism fails (lock, idempotency key, reconciliation, constraint) — amount at stake, data affected, users affected.

**Coverage**
| Domain | Priority | Status (`COVERED` / `COVERED (code only)` / `PARTIAL` / `BLOCKED`) | Passes | File |
|---|---|---|---|---|

**Out of scope**:

## 1. Glossary
Merged from the domain files; one row per business term.

| Business term | Exposed as | Name in code / data | Definition (one sentence) | Domain |
|---|---|---|---|---|

**Exposed as**: screen label (`UI`), API / webhook value (`API only`), `label / API value` (`both`), or `n/a` (internal term, or scope `none`). The column is always kept.

## 2. Actors, journeys & notifications
Merged from the domains' "Actors & permissions" tables. Permissions are what the enforcing guard allows, never what an annotation declares.

| Actor (user, admin, external system, worker) | Business objective | Effective roles & permissions (enforced by) |
|---|---|---|

**Journeys**: one line per end-to-end journey, chaining the domain flows it crosses (links only, no new analysis), with the business delays proven along the way.

**Notifications catalog** — merged from the domain files.

| Notification | Channel (email, webhook, UI) | Trigger | Recipient role | Domain / flow |
|---|---|---|---|---|

## 3. Business rules repository
Merged from all domain files, sorted by domain then risk.

| ID | Domain | Business formulation | Exact rule (thresholds, formulas, delays, values) | Dependencies | Source of truth | Code evidence | Integration evidence | Confidence & risk |
|---|---|---|---|---|---|---|---|---|

---

# Part II — Engineering

## 4. System boundaries, contracts & bridges
- Contact points, protocols, data stores (diagram).
- Real permissions matrix: what each role can do and the guard that enforces it (from the domains' "Actors & permissions"); with a UI in scope, UI vs API side by side.
- Key data mapping for P0: source vs target tables/entities (from the domains' "Data mapping").
- Write path and shared facts: how writes are committed and published, and the `S` facts every domain relies on.
- _(Multi-stack)_ bridge map between stacks: shared tables, sessions, queues, hooks, sync scripts, and the owner of each shared entity.

### Backend vs Frontend boundaries (consolidated — only when a UI is in scope)
- What lives only in the backend
- What is duplicated or validated on both sides
- What is purely frontend (with no backend guarantee)
- Main risks coming from this split

## 5. Business model, state machines & critical flows
- Per critical entity: one line + link to the domain file holding the state diagram.
- Per P0 flow: one line + link to the domain file holding the sequence diagram.

## 6. Invariants & failure modes audit (P0/P1)

| Domain | I1 | I2 | I3 | I4 | I5 |
|---|---|---|---|---|---|

Verdicts `PROTECTED` / `PARTIAL` / `ABSENT` / `UNKNOWN`. Below the table: the worst failure-mode findings across domains, one line each, linked to the domain file.

---

# Part III — Operations & support

## 7. Automations & reconciliation
- Inventory: crons, workers, queues, webhooks — trigger, retry policy, where failures land (retry queue, DLQ, log only, nowhere).
- Reconciliation mechanisms per bridge or external system, and gaps.

## 8. Observability & recovery runbook

| Stuck or failed state | How it shows (log, alert, nothing) | How to unblock (tool, command, back-office action) | Who can do it | Domain |
|---|---|---|---|---|

**Silent failures**: errors swallowed on a money path, with `[Code: …]` — each one is also a risk-matrix candidate.

**Degraded mode**: per external dependency, what happens when it is down (from the domains' "Degraded mode").

## 9. Technical debt, legacy pitfalls & gray areas
- Dead vs active code, hardcoded values, hidden configuration, integrity risks without constraints.
- `[CODE / OBSERVATION CONFLICT DETECTED]` items.
- _(Multi-stack)_ cross-system consistency risks.
- Open questions: one per gray area, blocking ones first, each with who can answer it.

---

## 10. Design Docs
_Only if requested in Phase 0._ One line per Design Doc: domain, decision needed, link to `design/<nn>-<slug>.md`.
