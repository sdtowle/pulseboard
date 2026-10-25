# PulseBoard Backlog

Format: user story, acceptance criteria (AC), MoSCoW priority (M/S/C/W), and the learning focus.
Size: S (≤ 1 session), M (a few sessions), L (a week+). Where possible, each story is a vertical slice: a thin piece of a feature built through every layer (database, server, UI) instead of one whole layer at a time. Unfamiliar terms are explained in [glossary.md](glossary.md).
Suggested GitHub labels: `epic:0`..`epic:4`, `must`, `should`, `could`, `wont`, `size:S/M/L`, `adr`, `spike`.

---

## Epic 0: Foundations

### 0.1 Repo conventions and tooling: **M** · S
As a developer, I want one agreed way of setting up the project (tools, folders, how to run it) so that every new feature starts from the same working starting point.
- AC: Language/runtime chosen and recorded in an ADR (see 0.2).
- AC: Formatter + linter configured and run via one command.
- AC: `.gitignore`, `.env.example`, and a README with "how to run" exist.
- AC: Branch and PR convention documented (small PRs, one story each).
- Learning: project layout, config vs code, env management.

### 0.2 ADR-001: backend runtime and database: **M** · S
As a developer, I want to write down why I picked my backend language and database so that I can explain the choice, and change it later if I need to.
- AC: `/docs/adr/001-*.md` compares at least 2 runtime options and 2 DB options (e.g. SQLite vs PostgreSQL) with trade-offs.
- AC: States the decision, the context, and consequences.
- Learning: decision-making, trade-off analysis. Hint: you already know JS, so is a Node backend the lowest-risk choice, or do you want to stretch?

### 0.3 Local dev environment: **S** · S
As a developer, I want one command that starts the database (and later Redis) so that I don't set them up by hand each time.
- AC: Documented start/stop procedure (Docker Compose or equivalent).
- AC: Fresh clone to running in under 10 minutes following the README.

### 0.4 Test setup: **S** · S
As a developer, I want a test runner set up so that every feature I build can come with tests.
- AC: One passing sample test runs via a single command.
- AC: Test DB strategy noted (separate DB or in-memory).

---

## Epic 1: Core Monitoring Engine (MVP)

### 1.1 Schema for monitors and check results: **M** · M
As a developer, I want to design the database tables so that monitors and their check history are saved reliably.
- AC: `monitors` table: identity, target URL, check interval, timeout, enabled flag, timestamps.
- AC: `check_results` table: monitor reference, checked-at, status code, latency, success flag, error message/type.
- AC: Explicit constraints: PKs, FKs with chosen delete behaviour, NOT NULLs, CHECKs (e.g. interval > 0).
- AC: Index strategy for "latest results per monitor" documented and justified.
- AC: Migration mechanism in place (versioned, repeatable).
- AC: Schema documented in `/docs/schema.md`.
- Learning: normalisation, constraints, indexes, migrations. Think about: what is a "result" when DNS fails and there is no status code?

### 1.2 Single HTTP check function: **M** · M
As the system, I want to check one URL over HTTP and get back a clear result (up or down, how long it took, and why it failed if it did).
- AC: Returns status code, latency, and success/failure with a typed failure reason.
- AC: Enforces a hard timeout; a hung server cannot hang the worker.
- AC: Distinguishes DNS failure, connection refused, TLS error, timeout, and non-2xx.
- AC: Redirect behaviour is an explicit, documented decision.
- AC: No DB access inside this function (pure I/O, easy to test).
- Learning: network error taxonomy, timeouts, separation of concerns. Hint: what do "up" and "down" actually mean?

### 1.3 Persist check results: **M** · S
As the system, I want each check result saved so that history can be looked up later.
- AC: A repository/data-access layer writes results and reads the latest N per monitor.
- AC: Writes are validated against the contract before insert.
- AC: Failed inserts are surfaced, not swallowed.
- AC: Integration test hits a real test DB.

### 1.4 Monitor runner (one-shot): **M** · M
As an operator (the person running PulseBoard), I want to run every active monitor once and store the results.
- AC: Loads enabled monitors, checks them with bounded concurrency, persists results.
- AC: One failing check does not stop the others.
- AC: Concurrency limit is configurable.
- AC: Summary printed (checked / up / down / errored).
- Learning: async concurrency, backpressure, partial failure.

### 1.5 CLI harness: **M** · S
As an operator, I want a command-line tool to add, list and remove monitors and to trigger checks.
- AC: Commands to add, list, enable/disable, and delete a monitor, and to run checks.
- AC: Bad input gives a clear message and non-zero exit code.
- AC: `--help` documents every command.

### 1.6 Result retention and pruning: **S** · S
As an operator, I want old results deleted automatically so that the database doesn't grow forever.
- AC: Configurable retention period; prune command deletes older rows safely.
- AC: Pruning runs in batches, not one giant delete.
- Learning: time-series data lifecycle.

### 1.7 Uptime and latency aggregates: **S** · M
As a user, I want uptime percentage and typical/slow response times over a time window so that I can judge how healthy a service is.
- AC: Computed for 24h / 7d / 30d windows per monitor.
- AC: Computation location (SQL vs app) chosen and recorded in an ADR.
- AC: Handles monitors with no data without dividing by zero.

---

## Epic 2: Management API and UI

### 2.1 HTTP server and routing: **M** · M
As a developer, I want a simple, consistent way to add new API endpoints so that the server stays organised as it grows.
- AC: Router module with method + path matching, params, and 404/405 handling.
- AC: Central error handler returns a consistent JSON error shape.
- AC: Request logging with a request ID.
- AC: Health endpoint (`/healthz`).
- Learning: request/response lifecycle, middleware pattern.

### 2.2 Monitor CRUD API: **M** · M
As a user, I want API endpoints to create, view, edit and delete monitors.
- AC: Create, read (list + single), update, delete with correct status codes (201, 204, 404, 409, 422).
- AC: Input validation with field-level error messages; URL scheme allow-list.
- AC: Pagination on list.
- AC: API contract documented (OpenAPI or markdown in `/docs`).
- Learning: REST semantics, validation, idempotency (PUT vs PATCH).

### 2.3 SSRF protection for monitor targets: **M** · M
As an operator, I want PulseBoard blocked from checking internal or private addresses so that users can't use it to snoop on my network.
- AC: Targets resolving to private/loopback/link-local ranges are rejected, including after redirects and DNS rebinding considered.
- AC: Behaviour documented in an ADR with the threat model.
- Learning: security thinking. This one matters because the product fetches user-supplied URLs.

### 2.4 User registration and login: **M** · L
As a user, I want an account so that my monitors are private.
- AC: Passwords hashed with a slow, salted algorithm (never plain/fast hash).
- AC: Session or token approach chosen via ADR (cookie session vs JWT trade-offs).
- AC: Login failure messages don't reveal whether the email exists.
- AC: Logout invalidates the session.
- Learning: auth fundamentals, secure cookies, CSRF.

### 2.5 Auth middleware and tenant scoping: **M** · M
As a user, I want to see and change only my own monitors.
- AC: Protected routes reject unauthenticated requests (401).
- AC: Every monitor/result query is scoped by owner; accessing another user's resource returns 404.
- AC: Test proves user A cannot read/update/delete user B's monitor.
- Learning: authorisation vs authentication, IDOR.

### 2.6 Rate limiting: **S** · M
As an operator, I want limits on how often a client can call the API so that one client can't overwhelm it.
- AC: Per-IP and per-user limits; stricter on login.
- AC: 429 with `Retry-After`.
- AC: Algorithm choice (fixed window, sliding window, token bucket) recorded.

### 2.7 Dashboard: monitor list view: **M** · M
As a user, I want to see all my monitors and their current status.
- AC: Vanilla JS, no framework; renders list from the API.
- AC: Loading, empty, and error states handled.
- AC: Accessible markup (landmarks, focus handling, status not conveyed by colour alone).

### 2.8 Dashboard: add/edit/delete monitor: **M** · M
As a user, I want to manage monitors from the UI.
- AC: Form with client-side validation mirroring server rules; server errors shown per field.
- AC: Delete requires confirmation.
- AC: Optimistic or pessimistic UI update choice documented.

### 2.9 Proxy-based reactive store: **M** · M
As a developer, I want a small store that tells the UI when data changes so that the screen updates automatically.
- AC: `Proxy`-based store with subscribe/unsubscribe and batched notifications.
- AC: Handles nested updates deliberately (documented decision).
- AC: Unit tests including unsubscribe and re-entrancy cases.
- Learning: leveraging your frontend strength to build the foundation you'll reuse in Epic 3.

### 2.10 Dashboard: monitor detail with latency chart: **S** · L
As a user, I want a latency/uptime chart so I can spot trends.
- AC: Lightweight chart (SVG or canvas, no chart library).
- AC: Handles gaps (missing checks) and large point counts via downsampling.
- AC: Time range selector (24h / 7d / 30d).

---

## Epic 3: Caching, Pub/Sub and Real-Time Telemetry

### 3.1 ADR-00X: caching strategy: **M** · S
As a developer, I want to decide and write down what gets cached, for how long, and when it is cleared, before adding Redis.
- AC: Defines what is cached (latest status), keys, TTLs, and invalidation rules.
- AC: Names the staleness you accept and why.

### 3.2 Redis latest-status cache: **M** · M
As a user, I want status lookups to be fast without querying the main database every time.
- AC: Worker writes latest status to Redis after each check.
- AC: API reads cache first, falls back to DB on miss.
- AC: App still functions (degraded) when Redis is down.
- Learning: cache-aside, failure modes.

### 3.3 Pub/Sub for status changes: **M** · M
As the system, I want a message sent whenever a monitor's status changes so that other parts can react without constantly asking (polling).
- AC: Worker publishes a status event when a monitor's state changes.
- AC: Event schema is versioned and documented.
- AC: Subscriber reconnects after connection loss.

### 3.4 Live updates to the dashboard (SSE first): **M** · L
As a user, I want the dashboard to update live.
- AC: SSE endpoint authenticated and tenant-scoped; only sends the user's events.
- AC: Client reconnects with backoff and resyncs state on reconnect.
- AC: Connections are cleaned up when clients disconnect.
- Learning: long-lived connections, raw protocol mechanics.

### 3.5 WebSocket gateway: **C** · L
As a developer, I want to also build a WebSocket version so that I understand how it differs from SSE.
- AC: Handshake and framing handled via a minimal approach (or a thin library, decision recorded).
- AC: Heartbeat/ping-pong detects dead connections.
- AC: Comparison written up in an ADR.

### 3.6 Scheduler: **M** · L
As an operator, I want monitors checked automatically, each at its own interval.
- AC: Each monitor runs at its configured interval without drifting badly.
- AC: Slow checks don't cause overlapping runs of the same monitor.
- AC: Graceful shutdown finishes or abandons in-flight checks cleanly.
- AC: Jitter/spread so checks don't all fire at the same instant.

### 3.7 Job queue and worker separation: **S** · L
As an operator, I want the part that decides what to check kept separate from the parts that do the checking so that I can add more checkers.
- AC: Scheduler enqueues jobs; workers consume them.
- AC: Retries with limits, and a dead-letter path for repeatedly failing jobs.
- AC: Two workers running concurrently never execute the same job twice (or the duplication is explicitly handled).
- Learning: distributed systems basics, at-least-once vs exactly-once.

### 3.8 Dashboard performance pass: **C** · M
As a user, I want the dashboard to stay smooth with hundreds of monitors.
- AC: Rendering speed measured before and after the change (browser Performance panel), rendering updates batched and only changed rows touched.

---

## Epic 4: Incident Management and Alerting

### 4.1 Incident model and lifecycle: **M** · M
As a user, I want an outage recorded as one incident with a start and end time, rather than many separate failures.
- AC: Schema for incidents linked to monitors; open/resolved states.
- AC: Incident opens after N consecutive failures and resolves after M consecutive successes (both configurable).
- AC: Timeline of state changes is retained.

### 4.2 Flapping detection: **S** · M
As a user, I don't want a flood of alerts from a service that keeps going up and down.
- AC: Flapping defined via a documented threshold (transitions in a window).
- AC: Flapping monitors suppress per-transition alerts and send a single "flapping" notice.

### 4.3 Webhook notifications: **M** · M
As a user, I want a webhook called when an incident opens or resolves.
- AC: Payload schema documented; requests signed (HMAC) so receivers can verify.
- AC: Retries with backoff; delivery attempts logged.
- AC: Webhook URLs get the same SSRF protection as monitors (see 2.3).

### 4.4 Email notifications: **S** · M
As a user, I want an email when my service goes down.
- AC: Provider abstracted behind an interface; a dev/mock provider for local use.
- AC: Duplicate alerts for the same incident are not sent.

### 4.5 Notification preferences: **C** · M
As a user, I want to choose channels per monitor.
- AC: Per-monitor channel configuration via API and UI.

### 4.6 Incident history UI: **S** · M
As a user, I want to see past incidents.
- AC: List with duration and cause; detail view with timeline.

---

## Cross-cutting (pull in as relevant)

| ID | Item | Priority |
|----|------|----------|
| X.1 | Structured logging and log levels | S |
| X.2 | Config validation at startup (fail fast) | S |
| X.3 | Graceful shutdown for server and worker | S |
| X.4 | CI pipeline (lint + tests on PR) | M |
| X.5 | Dockerfile and production-like run | C |
| X.6 | Metrics endpoint for PulseBoard itself ("monitor the monitor") | C |
| X.7 | Public status page | W (for now) |
| X.8 | Multi-region checking | W (for now) |

---

## Suggested order (first three sprints)

1. **Sprint 1, the smallest working start:** 0.1, 0.2, 0.4, 1.1, 1.2. Ends with a function that checks a URL, plus a schema.
2. **Sprint 2, MVP engine:** 1.3, 1.4, 1.5 (then 1.6 / 1.7 if time). Ends with a CLI that checks monitors and stores history.
3. **Sprint 3, first API feature:** 2.1, 2.2, 2.3. Ends with an API that manages monitors safely.

Finish each feature end to end (database, logic, and interface) before starting the next, rather than building all the databases first and all the screens later.
