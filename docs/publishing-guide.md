# Publishing the FlowGrid Technical Series

Open `index.html` to read all three formatted articles and view the separate figures. Each article also has an editable Markdown source.

## Article order

1. Building FlowGrid from API Request to Background Worker.
2. Making FlowGrid Jobs Recover from Crashes and Retries.
3. Adding Workflow Orchestration Scheduling and Live Monitoring to FlowGrid.

The drafts preserve the three implementation milestones. Phase 1 and Phase 2 describe the behavior at those milestones; Phase 3 describes the completed repository. The technical review is an author note, separate from the publication copy.

## Moving the copy into an editor

Use the rendered HTML article as the reading and copying source, and keep its Markdown file for editing. Set the article title and subtitle in the destination editor. Review heading levels, inline code, and Python, SQL, and JSON blocks after pasting; destination editors may handle rich text differently.

Upload each corresponding PNG where its figure appears. Keep the caption immediately below it and use the descriptive alt text from the Markdown image line. PNG is the upload copy; SVG is the editable master.

The local preview links between the articles are navigation aids. Once articles are published, add their actual published URLs to connect the series. No repository URL or publication URL has been invented.

## Figure placement

| Article | Figure | Upload file |
| --- | --- | --- |
| Part 1 | Architecture | `assets/images/01-phase-1-architecture.png` |
| Part 1 | Initial state machine | `assets/images/02-phase-1-state-machine.png` |
| Part 2 | Retry lifecycle | `assets/images/03-phase-2-recovery.png` |
| Part 2 | Attempt fencing | `assets/images/04-phase-2-attempt-fencing.png` |
| Part 3 | Workflow DAG | `assets/images/05-phase-3-workflow-dag.png` |
| Part 3 | Cron transactions | `assets/images/06-phase-3-cron-transactions.png` |
| Part 3 | Live event flow | `assets/images/07-phase-3-live-events.png` |
| Part 3 | Recorded throughput | `assets/images/08-phase-3-throughput.png` |

## Suggested tags

These are editorial suggestions, independent of any destination's current tag limits.

- Part 1: Python, FastAPI, Celery, PostgreSQL, Backend Development.
- Part 2: Distributed Systems, Reliability, Idempotency, Python, Backend Development.
- Part 3: Workflow Orchestration, Distributed Systems, PostgreSQL, Prometheus, Python.

## Evidence to retain

The technical review records corrected claims, source locations, and fresh SQLite test results. Preserve the distinction between those fresh checks and the historical PostgreSQL, smoke, chaos, and benchmark runs quoted in the articles. Any future performance claim should identify its workload, worker count, hardware, and measurement date in the same way.
