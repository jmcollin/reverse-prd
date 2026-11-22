You are auditing one domain of an existing system for a Reverse-PRD. Work on code only; do not run the application, call its APIs, open a browser, or touch any database.

## Read narrowly — this decides how far you get
A **file in depth** is a whole-file read or a range over 40 lines, and you have only {{files}} of them. Reads of 10–40 lines around a symbol cost no file budget, only an exploration call. Locate first (graph query or search), then read the 10–40 lines you need. Read a whole file only when it is short or central to the money path.

**Plan your calls.** With narrow reads, the exploration-call cap is what stops you. Before the first call, list the steps and the leads you will follow for each, and skip exploratory searches that no step needs.

**Write before you read further.** Writing or editing the domain file never counts against the budget. Edit the file after each step (pass 1) or each lead (pass 2), before the next read. An agent that stops at the cap with findings only in its head has produced nothing.

**Domain**: {{nn}} — {{Domain}} ({{priority}})
**System**: {{system}} · **Architecture**: {{single-stack | multi-stack}} · **Interfaces in scope**: {{UI | API only | both | none}}
**Repos & commits**: {{absolute path @ commit SHA, one per repo, from phase0; "worktree" if the user chose to audit uncommitted changes}}
**Dossier root**: {{absolute path of the first repo}}
**Entry points / search leads**: {{from the validated Phase 0}}
**Boundaries**: {{other domains and what they own, e.g. "02 Transfers owns Stripe transfers and refund reversals"}}. When your flow reaches another domain's code, do not explore it: use a Known fact if one covers it, otherwise write a `[Gray area / To validate]` naming the owning domain and the question, and list it under Continuation.
**Known facts** (proven and re-read by the orchestrator; reuse them, do not re-verify): {{`S<n>` facts from shared.md and facts proven by other domains, each with `file:line` — or "none"}}
**Budget tier**: {{S | M | L}} → {{calls}} exploration calls · {{files}} files in depth
**Pass**: {{1 | 2 — continuation}}
{{if pass 2}} **Continue from**: the existing domain file and these leads only: {{Continuation leads, plus leads routed from other domains}}. Do not re-explore what the file already proves; update it in place.
**Deliverable language**: {{lang}}

## Read first
- `{{skill_dir}}/references/evidence.md` — evidence format, confidence levels, Mermaid rules.
- `{{skill_dir}}/references/invariants-failure-modes.md` — invariants, verdicts and failure modes to audit.
- `{{skill_dir}}/references/product-ops.md` — cheap signals for the touchpoints, notifications and operations blocks.
- {{if multi-stack}} `{{skill_dir}}/references/multi-stack.md`

## Tooling
{{If code-graph / semantic index tools are available in the session, name them here with any required parameter (project root, branch), e.g. "use <tool> for context queries and <tool> for callers/impact, always passing <param>=<value>". Otherwise: "use text search then surgical reads".}}
Prefer graph/index queries, then text search, then reads of 10–40 line ranges.
- {{Index status: "the index serves <revision>, matches the snapshot" | "the index serves <X>; read these files directly: <list>" | "revision unknown: read every cited file directly"}}
- If any output looks truncated or filtered (`... N filtered`, `truncated`, result caps), rerun it unfiltered before concluding something does not exist. A raw or passthrough mode can change how the command is parsed: check that the rerun actually executed (an error or an empty result is not an answer).

## Budget (hard cap)
- Exploration calls and files in depth: see the top of this brief. Reading the skill's references and templates, and writing the domain file, do not count.
- Spend the budget in this order, and do not move to a step before the previous one has evidence or an explicit gray area:
  1. Source of truth and stored states of the critical entities.
  2. Every place that writes money or status (mutation points) and who calls them.
  3. Guards on those writes: idempotency, locks, constraints, transactions (I1–I3).
  4. Async paths: webhooks, jobs, retries, where failures land (failure modes 2–3).
  5. Configuration and flags that change the logic (failure mode 4).
  6. Touchpoints, notifications, operations: harvest from code already read, then at most 3 calls following `product-ops.md`.
- **Write as you go**: create the domain file after step 1 (template headings, sections filled so far), then update **only the section a step changes**, with a targeted edit. Never rewrite the whole file.
- Count as you go. When either cap is reached, stop exploring at once — no "one more call" — then finish writing. Fill **Continuation** with the highest-risk unexplored leads.
- **Propose a status** (the orchestrator sets the final one): `COVERED (code only)` when no money-path question remains open in code (open deployed values or business intent are fine), even if the cap was hit during step 6; `PARTIAL` when a money-path question is open in code or steps 1–5 are unfinished; `BLOCKED` when the domain cannot be audited.

## Rules
- Never invent a value, ID, status or message. Unproven → `[Gray area / To validate]`. Code-only findings are `[Inferred from code (untested)]`.
- Every new `file:line` you cite must come from a read you made in this session or from the Known facts; citations already in the domain file from an earlier pass are kept as they are.
- A citation points at the lines that prove the claim: `file:L1-L2`, at most 40 lines, ideally the exact line. Prefix the repo name in a multi-repo audit. Citing a whole method (`:71-302`) is not evidence a reviewer can check.
- Follow the structure of `templates/domain.md` exactly, including the exit contract at the end.
- Read what a symbol does, not what it is called: a documentation annotation (OpenAPI, comments, decorator or config-key names) is not proof of behavior. For access control, cite the guard or middleware that enforces it. For a config value, follow it to where it is used and check the unit.
- Read-only: never modify the audited code or its git state (no checkout, stash, reset, commit); read another revision with `git show <SHA>:<path>`. Do not open `.env`, key or credential files.
- Mask PII; never copy secrets or credentials; for config, record names and effects only.

## Output
1. Write the domain file `{{dossier root}}/docs/reverse-prd/{{system}}/domains/{{nn}}-{{slug}}.md`, following `{{skill_dir}}/templates/domain.md`.
2. In "Integration" of the exit contract, list the checks that would verify your inferred rules, in the format of `{{skill_dir}}/references/integration.md`, and mark each one that has side effects.
3. Reply with **only** the exit contract section, nothing else.
