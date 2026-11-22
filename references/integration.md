# Integration observation

Goal: confirm with real behavior what the code suggests, and capture exact user-facing values (statuses, amounts, messages).

## Before observing

- Ask the user which **non-production** environment, URLs and test accounts/roles to use if Phase 0 did not settle it. Never guess, ask for or record credentials: role and account names only.
- Confirm the environment is not production (URL, banner, config). If in doubt, stop and ask.
- Note the deployed version or commit if the environment exposes it (footer, `/version`, health endpoint) and update the Snapshot in `phase0.md`. If it differs from the audited commit, a code/observation mismatch may be deployment drift: say so when tagging `[CODE / OBSERVATION CONFLICT DETECTED]`.
- List side-effecting steps (payment, payout, refund, email, order creation, webhook replay) and get explicit approval before each one.

## Check format (proposed by subagents)

`environment · role · steps · expected value · side effects: none | <what>`. Running a check turns it into captured evidence (below): the `environment`, `role` and `steps` give the URL or endpoint and the role; the observed values replace `expected value`. A check with side effects runs only after the user's explicit approval (SKILL.md Step 5).

## Means, by preference

1. **Read-only UI or API calls** on the test environment: browser automation tool if available, otherwise HTTP requests.
2. **Read-only database queries** on a test database, if the user provides access. Check the schema before writing a query; never assume a column exists.
3. **Logs and monitoring** of the test environment.
4. **User-provided observations** (screenshots, exports, values pasted by the user). Tag them `[Integration: user-reported · ...]`.

Run checks one at a time. Several sessions on the same test accounts in parallel corrupt each other's state.

## What to capture

`URL or endpoint · role/account type · business ID · exact observed values` — status labels and codes, amounts with currency and rounding, dates/delays, error messages verbatim. Mask PII; never record tokens, cookies or passwords.

## When observation is impossible

Leave the rule at `[Inferred from code (untested)]` and add the check to the open questions with what would confirm it. Do not paraphrase code as if it were an observation.
