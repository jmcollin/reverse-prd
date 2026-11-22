# Design Doc — {{nn}} {{Domain}}

_Source: [domain file](../domains/{{nn}}-{{slug}}.md) · Dossier: [README](../README.md) · Commit(s): {{sha per repo, from phase0}} · Status: Draft (written by the skill) | Validated (the decision owner agreed) · Decision owner: {{role, from the dossier's open questions, else [Gray area / To validate]}} · Date: {{YYYY-MM-DD}}_

> Sections 1–8 are written for a product manager: no code, no class or table names, money and users first.
> **Facts**: every statement about today's behavior cites its source — a rule ID (`R{{nn}}.x`), an invariant (`I1`–`I5`), a shared fact (`S<n>`) or a domain — and keeps the confidence tag it has in the Reverse-PRD. No ID, no claim.
> **Proposals**: options, recommendation, rollout, target flows and "after change" verdicts are new by nature. Tag each one `[Proposal]` and cite the ID it addresses. No number (amount, frequency, effort, metric) that is not in the dossier; an estimate is labeled `estimate`.

## 1. In short (5 lines max)
- What this domain does for users / the business today.
- What is wrong or risky, in money, users or data.
- What we recommend.
- The decision we need, from whom, by when.

## 2. Problem
Who is affected, how often, what it costs today, and the worst case if the current protection fails. Cite the risk-matrix row of the dossier and the IDs behind it.

| Risk | Source (R / I / S / domain) | Confidence | Impact | Impact if broken |
|---|---|---|---|---|

## 3. Goals / Non-goals
- Goals: measurable where possible.
- Non-goals: what this change deliberately leaves alone.

## 4. Today (as-is)
One paragraph in business terms + link to the diagram in the domain file. Do not redraw it here.

## 5. Options `[Proposal]`
| Option | What changes for users | Risk removed (R / I / S) | Effort S/M/L (estimate) | Downsides |
|---|---|---|---|---|
| 0 — Do nothing | | | | |

**Recommendation** `[Proposal]`: option + one-line why.

## 6. Impact
Users · support / ops · finance / accounting · other systems · existing data (migration, backfill).

## 7. Rollout, success & rollback `[Proposal]`
- Steps (feature flag, migration, backfill), in order.
- The signal that proves it works (metric, reconciliation report, alert).
- How to roll back, and what happens to data written in between.

## 8. Decisions needed
| # | Question | Options | Owner | Blocks |
|---|---|---|---|---|

---

## Technical annex (engineering)

### Responsibility boundaries
- Owns:
- Does not own:

### Contracts
Only shapes read in code or observed, each with its evidence (`[Code: …]` / `[Integration: …]`) and the rule ID it supports. Anything else is `[Gray area / To validate]` — never a plausible payload.

| Contract (API / event / data model) | Shape or fields | Rule / invariant | Evidence | Confidence |
|---|---|---|---|---|

### Target flows `[Proposal]`
Sequence diagram of the *proposed* behavior, failure paths included. Follow the Mermaid rules in `references/evidence.md`.

```mermaid
sequenceDiagram
```

### Invariants before → after
| Invariant | Today (verdict, from the dossier) | After change `[Proposal]` | Mechanism |
|---|---|---|---|
| I1 Double processing | | | |
| I2 Forbidden negative/zero | | | |
| I3 Record ↔ audit log | | | |
| I4 Cross-system divergence | | | |
| I5 Orphans without FK | | | |

### Open technical questions
Numbered; each one names who can answer it.
