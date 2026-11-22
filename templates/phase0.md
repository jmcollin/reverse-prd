# Phase 0 — Exploration plan: {{system}}

_Date: {{YYYY-MM-DD}} · Deliverable language: {{lang}} · Dossier root: {{first repo}}/docs/reverse-prd/{{system}}/_

## Snapshot
The code as audited. Every `[Code: …]` in the dossier refers to these commits.

| Repo | Branch | Commit SHA | Uncommitted changes (outside `docs/reverse-prd/`) | Audit target |
|---|---|---|---|---|

**Audit target**: `commit` by default. When there are uncommitted changes, ask the user: `commit`, or `worktree` (citations then marked `worktree`, not reproducible from the SHA).

**Tooling & index**: {{code-graph tools found, or "none"}} · index serves {{revision, or "unknown"}} · matches the snapshot: {{yes | no — files to read directly | unknown — read every cited file directly}}.

Integration environment(s): {{name · URL · deployed version/commit if exposed, else "unknown"}}. If the deployed version differs from the audited commit, say so: observations may then disagree with the code.

## Architecture mode
`single-stack` or `multi-stack`, and whether there are several sources of truth for the same fact. One line of justification.

## System boundary
Main components, data stores, queues, external APIs, hooks. Name every dual source of truth already spotted.

**Interfaces in scope**: `UI` | `API only` | `both` | `none`. It sets the glossary column "Exposed as": the screen label (`UI`), the value an API client or webhook receiver gets (`API only`), `label / API value` (`both`), or `n/a` (`none`). The column is always kept.

**Shared infrastructure**: components every domain goes through (event-store or ORM wrapper and the code that commits and publishes writes, queue library, HTTP client, alerting, auth guard, idempotency helper). Audited once before the domains, in `shared.md`, on the orchestrator's budget; they belong to no domain's boundary.

| Component | Path | Why it matters (write path, concurrency, retries, idempotency, alerting, auth) |
|---|---|---|

## Domains
| # | Slug | Domain | Priority (P0/P1/P2) | Why | Entry points / search leads (symbols, tables, routes, events) | Owns (boundary) | Size signals | Budget tier |
|---|---|---|---|---|---|---|---|---|

- **#** and **Slug** name the domain file: `domains/<nn>-<slug>.md` (two-digit number, kebab-case name).
- **Owns (boundary)**: the code and decisions this domain is responsible for, so two domains never audit the same path (e.g. "refund reversal in Stripe" belongs to Transfers, not Commissions).
- At least 3 P0 domains when the system has money flows; otherwise justify. A domain list given by the user overrides this rule.
- **Size signals**: counts from cheap queries only — entry points, files matched by the leads, async paths (jobs, webhooks, listeners), stacks touched.
- **Budget tier** (see SKILL.md): `S` small and local, and default for P1/P2; `M` default for P0; `L` several async paths or two stacks. A domain bigger than `L` is split here into sub-domains (e.g. payouts → calculation / execution / reconciliation), each with its own row.

## Execution
Subagent per domain | inline — and why.

## Integration prerequisites
Non-production environment(s) and their URLs, **role names and test-account names only** (no emails, passwords, tokens, or URLs carrying secrets), test payloads without real customer data, and the side-effecting checks that will need approval. Unknowns are listed as questions.

## Scope
- In scope:
- Out of scope:

## Design Docs requested?
Yes / No (default: No). If yes: which domains.

## Questions for validation
Numbered, each answerable in one line.
