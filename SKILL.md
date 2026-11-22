---
name: reverse-prd
description: Reverse-engineers an existing, legacy or multi-stack application (e.g. PrestaShop + Laravel hybrid, NestJS services, several services sharing data) into an audited Reverse-PRD — cross-evidenced business rules, Mermaid state machines and sequence diagrams, invariant and failure-mode audit of money flows, Gherkin scenarios, optional PM-readable Design Docs. Use when asked for a reverse PRD, a functional audit of payment / payout / commission / refund flows, or product documentation of an undocumented system.
argument-hint: "[repo path(s)] [P0 domains...] [lang=<language>] [design-docs=true]"
disable-model-invocation: true
---

# Reverse-PRD

Produce a traceable Reverse-PRD of an existing system: what it really does, proven by code and by observation, focused on the risks that cost money or corrupt data. The dossier is used to make product and engineering decisions, so a visible gap is always better than a plausible guess.

All `references/` and `templates/` paths below are relative to this skill's directory (`${CLAUDE_SKILL_DIR}`), not to the audited repo.

## Inputs

Parse `$ARGUMENTS`:
- **Repo path(s)**: default to the current working directory. Several paths = multi-service system. The **first** repo is the dossier root.
- **Domains**: optional list of P0 domains. A list given by the user overrides the "at least 3 P0 domains" rule of Phase 0; record it as the justification.
- **`lang=`**: language of the deliverable. Default English. Templates stay in English; translate headings when writing in another language.
- **`design-docs=true`**: also produce Design Docs (Step 7). Default false; can also be requested when validating Phase 0.

## Non-negotiables

1. **Never invent evidence.** A value, ID, status or message not actually read or observed is written as `[Gray area / To validate]`. Fabricated precision is the worst failure of this skill.
2. **Read-only by default.** Do not modify the audited code, data, configuration or git state (no checkout, stash, reset or commit). Read another revision with `git show <SHA>:<path>`. The one write allowed in an audited repo is the dossier folder `docs/reverse-prd/<system>/` of the first repo.
3. **No production.** Integration observations happen only on test, staging or local environments. Any action with side effects (payment, payout, refund, email, webhook replay, order creation) needs explicit user approval first, and only on a non-production environment. A check proposed by a subagent is never an approval.
4. **No sensitive data in the dossier or the chat.** Mask PII (emails, names, addresses, phone, IBAN, card data); never write or ask for secrets, tokens, passwords or credentials, and do not open `.env`, key or credential files. For configuration, record variable/flag *names* and their effect, never values that are secret.
5. **Write to files, not to the chat.** The dossier is long; files survive context compaction. The chat only carries status, paths and questions.

## Tooling

Use what the session provides, in this order:

1. **Code-graph / semantic index tools**, if any are available (symbol search, callers/callees, impact analysis, natural-language context queries). Check the tool list; read their usage notes; pass any required project-root parameter.
2. **Text search** with whatever search tool the environment offers.
3. **Surgical reads**: line ranges of 10–40 lines around the symbol. Read a whole file only when it is short or central.
4. **Integration observation**: see `references/integration.md`.

Two guardrails, whatever the tools:
- **The index must match the snapshot.** An index can be stale, or serve another branch or commit than the one audited (fallback to the default branch is common). In Step 1, record in Phase 0 which revision the index serves. If it differs from the snapshot, compare the two with `git diff --stat` and read the differing files directly. If the tool reports no revision, treat it as unknown and read every cited file directly. Evidence from the wrong revision is fabricated evidence.
- **Inventories must be complete.** Output compressors, proxies, pagers and result limits can truncate or filter output silently (`... N filtered`, `truncated`, default result caps). An inventory with holes — crons, listeners, consumers, endpoints, events — hides exactly the paths this audit looks for. When output shows any sign of truncation, rerun it unfiltered (the tool's raw or passthrough mode, a higher limit, or a narrower path) before concluding something does not exist. A raw mode can parse the command differently (quoting, globbing, braces): check that the rerun actually executed — an error or an empty result is not an answer. For complex patterns, prefer a pattern file over inline quoting.

## Budget

The budget protects context and stops runaway exploration; it is not a quota to fill. Elapsed time is not observable, so tool calls are the unit.

| Tier | Exploration calls | Files in depth | Typical domain |
|---|---|---|---|
| S | 20 | 4 | Small, local, one stack, no async path; default for P1/P2 |
| M | 35 | 7 | Default for P0; every continuation pass |
| L | 50 | 10 | Several async paths, or two stacks |

- **What counts**: exploration calls only — searches, graph queries, reads of code. Reading the skill's references and templates, and writing or editing the domain file, never count. The shared-infrastructure pass (Step 3) is charged to the orchestrator, not to any domain.
- **Sized in Phase 0** from cheap signals (entry points, files matched by the leads, async paths, stacks touched) and validated by the user. A domain bigger than `L` is **split** into sub-domains in Phase 0 rather than given more budget.
- **Files in depth** = whole-file reads or ranges over 40 lines. Surgical reads of 10–40 lines count only as calls: the cap limits dumping files into context, not how far the analysis can reach. With narrow reads the call cap binds first, so agents plan their calls before the first one.
- **Risk-first order**: source of truth → money/status writes → guards (I1–I3) → async paths → config → product/ops blocks (harvested, at most 3 dedicated calls). Exact steps in `templates/subagent-brief.md`. When the budget runs out, what is missing is the least risky part.
- **Write as you go**: the domain file is created after step 1, then each step (or each lead in a continuation pass) edits only the section it changes, before the next read — never a full rewrite, which costs the file's length again every time.
- **Hard stop**: at either cap, stop exploring at once, then write. Unexplored leads go to the exit contract's **Continuation**, highest risk first.
- **Cost drivers**, in order: number of calls (each one re-sends the growing context), whole-file reads, full-file rewrites, fresh agents re-reading the same material (continuation passes, rediscovered shared infrastructure). The rules above target each of them.

**Domain status** — proposed by whoever audits the domain, **set by the orchestrator** (Step 4), and written in the domain file's exit contract:
- `COVERED` — no money-path question open in code, and the integration checks were observed (set in Step 5).
- `COVERED (code only)` — no money-path question open in code; only deployed values, business intent or observations remain open. The best status of a code-only run. A cap hit during step 6 (product/ops harvest) alone does not prevent it.
- `PARTIAL` — a money-path question is still open in code, or steps 1–5 are unfinished. Triggers Step 4b for P0. After the continuation pass, a domain still in this state stays `PARTIAL` (final) and its leads become open questions.
- `BLOCKED` — the domain cannot be audited (code not found, access missing); one line why.

Domain statuses are not invariant verdicts: those are defined in `references/invariants-failure-modes.md`.

## Workflow

### Step 1 — Scoping (light)
Identify stacks, entry points, data stores, queues and external APIs, using only cheap queries (manifests, routing, schema files, graph overview). Record:
- the **snapshot** of each repo: branch, `git rev-parse HEAD`, and `git status --porcelain` (ignoring `docs/reverse-prd/`). If the tree has uncommitted changes outside that folder, ask the user whether to audit the working tree (citations then marked `worktree`) or the commit;
- the **index revision** served by code-graph tools (see Tooling);
- the **interfaces in scope** — `UI`, `API only`, `both` or `none` — which decides what "Exposed as" means in the glossary;
- **shared infrastructure** every domain goes through (event-store or ORM wrapper and the code that commits and publishes writes, queue library, HTTP client, alerting, auth guard): audited once, not once per domain.

If the system has several sources of truth or several stacks sharing data, read `references/multi-stack.md` now.

**Existing dossier.** If `docs/reverse-prd/<system>/` already exists, read its `phase0.md`, `shared.md` and coverage table, compare the recorded commits with the current ones, and ask the user:
- **Update**: keep domain files whose repos are unchanged; re-audit the domains the user names, and every domain whose repo commit changed, as a new pass 1 into the existing file. Facts in `shared.md` and observations from a changed repo are marked `stale` until re-verified at the new commit.
- **Start over**: move the old dossier to `docs/reverse-prd/<system>-<YYYY-MM-DD>/` before creating a new one.

### Step 2 — Phase 0 plan → STOP
Fill `templates/phase0.md`, write it to `docs/reverse-prd/<system>/phase0.md` in the first repo, show it to the user and **stop until they validate or amend it**. No deep exploration before validation.

### Step 3 — Prepare the dossier
In the first repo, create or (on update) reuse `docs/reverse-prd/<system>/` with:
- `README.md` — the consolidated dossier, from `templates/dossier.md`; every section holds `_pending_` until Step 6.
- `shared.md` — from `templates/shared.md` (when Phase 0 lists shared infrastructure).
- `domains/<nn>-<slug>.md` — one file per validated domain, from `templates/domain.md`.

`<system>` is a short kebab-case name of the audited system; `<nn>` is the two-digit domain number and `<slug>` the kebab-case domain name, both from the Phase 0 domain table. Tell the user the path.

Read `references/evidence.md`, `references/invariants-failure-modes.md` and `references/product-ops.md` before the first domain.

**Shared infrastructure first.** For each shared component listed in Phase 0, spend surgical reads (≈5–10 calls in total, orchestrator budget) on the facts that change every domain's verdicts: the **write path end to end** (what commits an aggregate or entity, which publisher or bus writes it, and whether that happens before or after external calls), write concurrency (expected version, locks, transactions), retry and dead-letter behavior, idempotency helpers, alerting (can an alert be lost?), and the access guard. Write each fact to `shared.md` with an ID (`S1`, `S2`…) and `file:line` evidence. They go into every brief as **Known facts**, so no domain spends budget rediscovering them. Shared components belong to no domain's boundary. Skip this step when Phase 0 lists no shared component.

### Step 4 — Domains
**Subagent mode** (default when a subagent tool is available and there are 2+ domains; follow any user or project rule that restricts subagents): dispatch one subagent per domain, in parallel, with `templates/subagent-brief.md` filled in. Replace every `{{…}}` placeholder; for `{{if …}}` lines, keep the line without the marker when the condition holds, delete it otherwise. Pass: `{{skill_dir}}` = `${CLAUDE_SKILL_DIR}`; the code-graph tools found in Tooling and the index status; the commits from Phase 0; the interfaces in scope; the **boundaries** — which other domain owns which code; and the **Known facts** from `shared.md`. Each subagent writes its own domain file and returns only its exit contract. Subagents do **code analysis only**: they list the integration checks to run instead of running them, because parallel browser/API sessions on shared test accounts interfere with each other.

**Inline mode** (no subagent tool, or a single domain): process domains one by one with the same template, the same budget (a fresh counter per domain and per pass) and the same exit contract. After each domain, keep only its exit contract in mind and re-read the domain file when needed.

**After each returned domain** (orchestrator):
1. **Check the file exists and follows `templates/domain.md`** (all sections, exit contract present). If the agent stopped before writing, write only the findings you re-read yourself at their cited lines, tagged as the agent did, and note in the file that the orchestrator applied them; anything you did not re-read goes to Continuation as a lead.
2. **Set the status** with the definitions above, and write it in the exit contract. When you downgrade a proposed `COVERED (code only)` to `PARTIAL`, write each open money-path question as a Continuation lead.
3. **Propagate facts.** Before a fact one domain proved enters `shared.md` or another domain's Known facts, re-read its cited lines; record its origin (`from domain <nn>, verified at consolidation`). Do not let a pass rediscover or re-ask what is already proven.
4. **Route cross-boundary leads.** A lead that belongs to another domain goes to that domain's continuation pass as a lead, or becomes a Known-facts request answered by you; it is never dropped.

### Step 4b — Continuation pass (P0 only)
For each P0 domain whose orchestrator-set status is `PARTIAL`, run **one** more pass with the same brief marked `Pass: 2`, its Continuation leads plus any leads routed from other domains, tier `M`, and the Known facts updated with everything proven since pass 1. It updates the existing domain file in place and does not re-explore what is already proven. Then apply "After each returned domain" again; no third pass. P1/P2 domains stay `PARTIAL`. Tell the user in one line which domains got a second pass.

### Step 5 — Integration pass (sequential)
Collect the integration checks from all exit contracts, deduplicate them, and run them one at a time following `references/integration.md`:
- read-only checks may run on a confirmed non-production environment;
- every check with side effects is shown to the user, with its environment and expected effect, and runs only after their explicit approval;
- ask the user only for the environment URL, the role names and the test accounts to use — never for credentials.

If no environment is reachable, ask the user once for observations; otherwise leave the rules at `[Inferred from code (untested)]`. Update each domain file: confidence levels, observed values, `[CODE / OBSERVATION CONFLICT DETECTED]` tags, and the status (`COVERED` when the remaining checks were observed).

### Step 6 — Consolidate
**Verify evidence first.** Do not trust subagent output blindly. For each domain, open the 1–2 key code evidence of its exit contract and the evidence of its highest-risk P0 rule, at the cited lines (≈3 surgical reads per domain), plus every `shared.md` fact that came from a domain.
- Judge a domain from its **domain file**, not from its exit contract alone: the contract is a summary and omits findings that the file holds.
- Reject citations wider than 40 lines: re-read and narrow them, or mark the claim unverified.
- Check what the cited code *does*, not what it is named: documentation annotations, comments, decorator and config-key names are the usual source of wrong claims (an OpenAPI scope read as an access check).
- If a citation does not say what the dossier claims, fix the row or downgrade it to `[Gray area / To validate]`. If two or more citations of the same domain fail: when the domain has had no continuation pass, run one now with the failed citations as leads; otherwise set it `PARTIAL` and tag its unverified rows `[Gray area / To validate]`, and tell the user.
- **Merge across domains**: the same root cause found by several domains (usually shared infrastructure) becomes one risk row citing the `S` fact and every domain; questions that duplicate or contradict each other are reformulated once, and the domain files are corrected.

Then fill `README.md` from the domain files and `shared.md`, part by part: Part I product (summary and risk matrix, glossary, actors / journeys / notifications, business rules), Part II engineering (boundaries, model and flows, invariants matrix), Part III operations (automations, runbook, technical debt and gray areas).

Before delivering, check:
- [ ] Every P0 rule has code evidence, or is tagged `[Gray area / To validate]` with a question.
- [ ] Every risk-matrix row cites its rule, invariant or shared-fact IDs (`R`, `I`, `S`).
- [ ] Every domain file follows the template and has an exit contract; the coverage table matches their statuses.
- [ ] Evidence spot-check done for every domain and every propagated `shared.md` fact; failures fixed or downgraded.
- [ ] Every Mermaid block follows the rules in `references/evidence.md`.
- [ ] No PII, secret, token or credential anywhere in `docs/reverse-prd/<system>/`.
- [ ] Every silent failure on a money path appears in the runbook (section 8) and the risk matrix.
- [ ] Every blocking question appears in section 9, with who can answer it.

### Step 7 — Design Docs (optional)
Only if `design-docs=true` or requested in Phase 0. Write one Design Doc per requested P0 domain to `docs/reverse-prd/<system>/design/<nn>-<slug>.md`, from `templates/design-doc.md`, and list them in dossier section 10.

- **Audience**: a product manager first. Sections 1–8 contain no code, class or table names; the technical annex holds contracts, target flows and invariants.
- **Facts vs proposals**: everything about current behavior (problem, as-is, contracts, invariants today) comes only from the Reverse-PRD, with its rule, invariant or shared-fact ID and confidence tag — no ID, no claim. Everything new (options, recommendation, rollout, target flows, invariants after change) is tagged `[Proposal]` and cites the ID it addresses. No number (amount, frequency, effort, metric) that is not in the dossier; estimates are labeled as such.
- Contracts list shapes read in code or observed, never plausible payloads. Link the domain file's diagrams instead of redrawing the current behavior.

### Final message
Dossier path (and Design Doc paths when produced), 5-line summary of the major risks, and the open questions that block a domain.
