# Product & Ops signals

Read before the first domain. Purpose: fill the **Touchpoints**, **Notifications** and **Operations** blocks of a domain cheaply. First harvest from code already read for the money path; then spend **at most 3 tool calls per domain** on the targeted searches below. Never let these blocks eat the budget of the P0 audit.

## Product (PM)

- **Notifications**: mailer / notification classes, email template names, event listeners that send messages, outgoing webhook dispatchers, flash / toast messages. Record trigger, recipient role and template name — never template content containing PII.
- **Eligibility & visibility**: guards in controllers, voters, policies and middlewares; template conditionals that show or hide an action; validation rules and their messages (verbatim).
- **Delays & SLA**: constants and config with time units (`+N days`, `DateInterval`, `addDays`, TTL / expiry columns), cron schedules. A business delay with no constant, cron or config behind it is `[Gray area / To validate]` with a question for the PM.
- **Exposed values** (glossary "Exposed as"): with a UI in scope, translation files map stored status values to screen labels — the cheapest source. API-only: response DTOs, serializers and outgoing event / webhook names show what clients receive. Skip when nothing in scope is exposed.

## Ops / support

- **Recovery tools**: console commands (`artisan`, Symfony `Command`, `bin/console`, NestJS CLI, `scripts/`), admin controllers or back-office buttons with replay / retry / resync / force-status actions. Note who can run them.
- **Observability**: logger calls on failure paths (level, and whether the business ID is logged), error tracking / APM captures, metrics, alert hooks.
- **Silent failures**: `catch` blocks that only return or continue, exceptions swallowed without a log, PHP `@` suppression, `.catch(() => {})`, `except: pass`, empty job `failed()` handlers, exhausted retries with no DLQ. Each one is a candidate risk row with `file:line`.
- **Degraded mode**: timeouts and fallbacks on external calls, circuit breakers, kill switches / maintenance flags.
