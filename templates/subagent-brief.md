You are auditing one domain of an existing system for a Reverse-PRD. Work on code only; do not run the application, call its APIs, open a browser, or touch any database.

**Domain**: {{nn}} — {{Domain}} ({{priority}})
**System**: {{system}} · **Architecture**: {{single-stack | multi-stack}}
**Repos**: {{absolute paths}}
**Entry points / search leads**: {{from the validated Phase 0}}
**Deliverable language**: {{lang}}

## Read first
- `{{skill_dir}}/references/evidence.md` — evidence format, confidence levels, Mermaid rules.
- `{{skill_dir}}/references/invariants-failure-modes.md` — invariants and failure modes to audit.
- {{if multi-stack}} `{{skill_dir}}/references/multi-stack.md`

## Tooling
{{If code-graph / semantic index tools are available in the session, name them here with any required parameter (project root, branch), e.g. "use <tool> for context queries and <tool> for callers/impact, always passing <param>=<value>". Otherwise: "use text search then surgical reads".}}
Prefer graph/index queries, then text search, then reads of 10–40 line ranges. Budget: about 5 source files read in depth and about 30 tool calls; when spent, stop and mark the domain `PARTIAL`.

## Rules
- Never invent a value, ID, status or message. Unproven → `[Gray area / To validate]`. Code-only findings are `[Inferred from code (untested)]`.
- Mask PII; never copy secrets or credentials; for config, record names and effects only.
- Do not modify the audited code.

## Output
1. Write the domain file `{{absolute path}}/docs/reverse-prd/{{system}}/domains/{{nn}}-{{slug}}.md`, following `{{skill_dir}}/templates/domain.md`.
2. In "Integration" of the exit contract, list the checks that would verify your inferred rules: `environment type · role · steps · expected value`.
3. Reply with **only** the exit contract section, nothing else.
