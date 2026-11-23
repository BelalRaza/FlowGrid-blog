# FlowGrid Technical Review and Validation

Reviewed November 23, 2026. This note is for the author; the three article files contain the publication copy.

## Scope and source snapshots

The review covered the supplied project README and three phase posts, then traced their principal claims through the implementation, tests, Compose configuration, and recorded benchmark results. The original repository and source posts were left unchanged.

The repository was clean when checked. Its milestones are:

- Phase 1: `24fea3f` — Working product.
- Phase 2: `5005b30` — Reliability.
- Phase 3: `3435daf` — Advanced system design; the current HEAD reviewed.

The three committed snapshots were extracted into isolated working directories. Tests used the existing Python 3.11.13 environment with Celery 5.7.0 and SQLAlchemy 2.0.58. These are fresh checks of historical source using the available environment, rather than reproductions of each milestone's original dependency installation.

## Fresh automated checks

| Snapshot | SQLite result | Exit code |
| --- | --- | --- |
| Phase 1 | 30 passed | 0 |
| Phase 2 | 80 passed | 0 |
| Phase 3 | 121 passed, 1 skipped | 0 |

The skipped test requires PostgreSQL LISTEN/NOTIFY. Tests use Celery eager execution and a throwaway SQLite database, and do not exercise a real Redis transport or worker process. The environment emitted a Starlette/httpx deprecation warning without failing the suites.

The recorded PostgreSQL counts, migrations, smoke checks, chaos scenarios, browser observations, and throughput numbers were checked for consistency with the repository material. They were not rerun in this review. The articles identify those results as milestone build-log observations.

## Technical corrections applied to the articles

| Original claim or implication | Correct treatment | Implementation or primary reference |
| --- | --- | --- |
| Killing a worker necessarily loses a Celery countdown retry | ETA tasks can occupy worker memory, but acknowledgement and Redis redelivery behavior matter. Database scheduling is justified by visibility and explicit reconciliation. | Celery calling guide linked below; `app/services/retry.py`; sweeper release path |
| Crash recovery is bounded by timeout plus 15 seconds | That is the lease deadline measured from claim. Sweep delay, retry backoff, broker availability, queue wait, and replacement runtime also affect completion. | `app/services/jobs.py:383`; `app/services/sweeper.py:58` |
| An expired lease proves a worker is dead | Expiry is a recovery decision. A paused worker may resume; the outcome fence protects against stale authority. | `app/services/jobs.py:448` and `:468` |
| Exactly one cron target exists for every tick under all failures | Successful target creation commits atomically with the schedule advance. The exception handler advances separately and can skip a tick without a target. This is distinct from execution count. | `app/services/schedules.py:118`, especially `:148` |
| Every leftover Kahn node belongs to a cycle | Leftovers can also include nodes downstream of a cycle. | `app/services/workflows.py:84` |
| All workflow errors, including cycles, always return together | Structural and payload problems are collected. Cycle detection runs only if those checks produced no errors. | `app/services/workflows.py:147` |
| The DAG concurrency test exercises competing transactions | It invokes `advance_run` twice sequentially in one session. It establishes repeat-call behavior, not a threaded/database concurrency test. | `tests/test_workflows.py:197`, calls at `:206` and `:207` |
| A committed event always describes the current database row | NOTIFY follows commit, but the row can advance again before a client receives it. Events are refresh hints. | `app/db/triggers.py`; PostgreSQL NOTIFY reference below |
| Hundreds of streams need only one database connection in total | The listener is shared per API process. REST requests, initial authentication, and periodic revocation checks still use connections. | `app/services/events.py`; `app/api/events.py:53` |
| Every database update emits a live event | The trigger emits for inserts and selected status or attempt changes. Ordinary unrelated field updates need not notify. | `app/db/triggers.py` |
| Notifications were actually sent | The handler inserts deduplicated notification rows. There is no real delivery integration. `notifications_sent` counts new rows. | `app/tasks/handlers.py:228` |
| Request idempotency expires precisely at 24 hours | The sweeper removes rows older than the TTL. Until deletion, the existing record can continue to match. | `app/services/sweeper.py:187`; `app/services/jobs.py` |
| A stale queued job is republished at most once per interval across all sweepers | Timestamp refresh reduces repeated publication by a sweeper; the publish occurs before the guarded timestamp update, so competing sweepers may both publish. | `app/services/sweeper.py:141` |
| Queue republish always uses a 300-second threshold | 300 seconds is the application default. Compose sets 60 seconds. | `app/core/config.py`; `docker-compose.yml:11` |
| The 1 MB request limit covers every body | It is a 1,000,000-byte Content-Length check, not a streaming cap on uploads without that header. | `app/core/middleware.py:62` |
| Every handler runs without holding a database connection | The executor closes its claim session before invoking a handler. The record-processing handler opens its own session for effects. The article narrows the claim to executor transactions. | `app/services/executor.py`; `app/tasks/handlers.py:236` |
| Slower no-op scaling proves PostgreSQL is the bottleneck | The shared-VM measurements show sublinear scaling; they do not isolate the limiting component. | `docs/benchmarks/phase-3-results.md`; `scripts/benchmark.py` |
| Phase 1 publication exceptions prove no message was delivered | A broker error can be ambiguous. The FAILED transition is guarded by PENDING and cannot overwrite a worker that already advanced the row. | Phase 1 `app/services/jobs.py:63` |

## Recorded benchmark consistency

The benchmark artifact is dated November 20, 2026, at 22:15 UTC. All services share an eight-CPU Docker VM; each worker container has two processes.

| Worker containers | No-op jobs per second | Simulated I/O jobs per second | I/O p50 / p95 completion seconds |
| --- | --- | --- | --- |
| 1 | 256.6 | 9.1 | 22.17 / 41.94 |
| 2 | 396.0 | 17.9 | 11.31 / 21.26 |
| 4 | 466.2 | 35.2 | 5.82 / 10.89 |

The total workload is `(1,000 + 400) × 3 = 4,200` jobs. Ideal simulated-I/O throughput is 10, 20, and 40 jobs per second. The measured efficiencies are 91.0%, 89.5%, and 88.0%. The article uses the artifact's unrounded no-op values and labels the run as a local measurement.

## Primary documentation consulted

The revised ETA discussion follows the [Celery ETA and countdown documentation](https://docs.celeryq.dev/en/stable/userguide/calling.html#eta-and-countdown).

The state-transition and sibling-completion explanations use [PostgreSQL 16 transaction isolation](https://www.postgresql.org/docs/16/transaction-iso.html), matching the repository's PostgreSQL 16 Compose image.

The event-delivery wording follows [PostgreSQL 16 NOTIFY semantics](https://www.postgresql.org/docs/16/sql-notify.html). The browser API boundary was checked against the [HTML EventSource interface](https://html.spec.whatwg.org/multipage/server-sent-events.html#the-eventsource-interface).

## Publication and artifact checks

Each article has a title, subtitle, explanatory prose, selected code, image placements, captions, and alt text. The figures are supplied as 2400-pixel-wide PNGs for upload, plus editable SVGs. The benchmark uses separate scales for no-op and simulated-I/O throughput.

The HTML copies and local article links were checked for readability, missing assets, horizontal overflow, and formatting. The figures were rendered and visually inspected. No content was uploaded to Medium.
