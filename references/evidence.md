# Evidence rules

## Evidence format

Each business rule, state transition or invariant verdict carries evidence:

- **Code evidence**: `[Code: <file>:<L1>-<L2> <Class::method>]` (or `<file>:<line>` for a single line). Paths and line numbers refer to the commit recorded in `phase0.md` (or to the working tree, marked `worktree`, when the user chose it); in a multi-repo audit, prefix the path with the repo name. The cited range covers the lines that prove the claim — 40 lines at most, ideally the exact line; a whole method or file is not a citation. In prose, follow the tag with a 5–15 line extract of those lines in a fenced block; in a table cell, put the tag only, and give the extract for P0 rules in the domain file's flow or rule notes.
- **Integration evidence**: `[Integration: <URL or API endpoint> · <role/account type> · <business ID> · <exact observed values>]`.

Both are expected for a rule of a `COVERED` domain. A `COVERED (code only)` domain carries code evidence alone. When only one exists, the confidence level says so — never fill the missing half with plausible content.

## Confidence levels

| Tag | Meaning |
|---|---|
| `[Verified UI/Code]` | Code read **and** behavior observed on an environment (through a UI or an API), consistent. A user-reported observation counts when it gives exact values; its integration evidence is tagged `user-reported`. |
| `[Inferred from code (untested)]` | Code read, no observation. Default for code-only subagent output. |
| `[Gray area / To validate]` | Neither proven by code nor observed (dynamic config, external service, unreachable code path). Add exactly one precise question and who can answer it. |

## Code / observation conflict

When code and observation (UI or API) disagree (different formula, feature disabled by a DB flag, dead branch actually active):

1. Do not pick one silently.
2. Document both, tagged `[CODE / OBSERVATION CONFLICT DETECTED]`.
3. Describe product behavior from the observation; record the code as technical debt (dossier section 9) with the likely cause (flag, config, override, deployment drift).

## Exact values

Record real identifiers: status codes and their numeric/string values, constants, thresholds, rounding modes, currencies, delays, cron schedules, error messages verbatim. Mask PII and secrets (see SKILL.md non-negotiables).

## Source of truth

For every critical rule — always for money — state where the value comes from: table/column, runtime calculation (method), external API, environment variable or flag name, admin configuration. If two places can hold the value, say which one wins and whether they can diverge.

## Dual formulation

Every critical rule has:
- **Technical**: formula or condition as implemented.
- **Business**: one sentence a product manager can validate without reading code.

## Open questions

One precise, answerable question per gray area (who can answer it, what value is expected). Mark the ones that block a domain.

## Mermaid rules

Broken diagrams are frequent; check before writing:
- `stateDiagram-v2` for states, `sequenceDiagram` for flows.
- State ids: ASCII letters, digits, underscores. Put human labels in `state "Label with spaces" as ID` declarations.
- Avoid `()`, `:`, `;`, `#`, `{}` and accents inside transition and message labels; prefer short labels and move detail to the text.
- Use `participant X as Label` for long participant names.
- One diagram per fenced ```mermaid block, nothing else inside.

## Architectural patterns

When relevant, note if the domain follows a recognizable pattern (MVC, layered, event-driven, CQRS, etc.) and any deviations that create risk (especially around consistency, validation duplication, or dual sources of truth).
