# Glossary

Plain-English meanings for jargon used in the backlog and issues. Add to it whenever a term trips you up.

## Process terms
- **User story:** a task written as "As a *who*, I want *what*, so that *why*". It makes you state why the work matters.
- **Acceptance criteria (AC):** the yes/no checklist that says a story is finished.
- **MoSCoW:** a priority scale. **Must** (the product doesn't work without it), **Should** (important, but there's a workaround), **Could** (nice to have), **Won't** (not now).
- **Epic:** a big theme that groups many stories.
- **Sprint:** a short, fixed block of work (here, whatever you choose, e.g. 1–2 weeks).
- **Backlog:** the full list of work not yet started.
- **ADR (Architectural Decision Record):** a short document noting a significant technical decision, the options you weighed, and why you chose one.
- **Spike:** a time-boxed experiment to learn something before committing to a design.
- **Vertical slice:** one feature built through every layer (database, server, UI) so it works end to end. The opposite is building all of one layer first, then the next.
- **Baseline / starting point:** the known-good setup every new piece of work begins from (same tools, same folders, same commands).
- **Cross-cutting:** applies to the whole project rather than one feature (logging, CI).

## Roles
- **Operator:** the person running and maintaining PulseBoard.
- **User:** the person using PulseBoard to watch their own services.
- **The system:** PulseBoard's own code acting on its own.

## Backend and data terms
- **Monitor:** one thing PulseBoard watches (e.g. a URL checked every minute).
- **Check / check result:** a single test of a monitor, and what came back.
- **Schema:** the design of your database tables, columns and how they relate.
- **Constraint:** a rule the database enforces itself (e.g. "this column can't be empty").
- **Migration:** a versioned script that changes the database structure, so changes are repeatable and tracked.
- **Index:** a lookup structure that makes queries on a column fast, at the cost of slower writes.
- **Time-series data:** records stamped with a time that mostly get appended (like check results).
- **Aggregate:** a summary computed from many rows (uptime %, average latency).
- **Latency (p50 / p95):** response time. p50 is the typical (median) request; p95 means 95% of requests were faster, so it shows the slow tail.
- **Retention / pruning:** how long you keep old data, and deleting what's older.
- **Concurrency:** doing several things at once (e.g. many checks in parallel). **Bounded concurrency** caps how many.
- **Timeout:** giving up after a set time instead of waiting forever.
- **Repository / data-access layer:** the one place in the code that talks to the database.
- **Fail fast:** crash immediately with a clear message on bad setup, rather than misbehaving later.
- **Graceful shutdown:** stopping by finishing or cleanly abandoning current work, not cutting it off.

## API and security terms
- **REST API:** a standard style of web API using URLs and HTTP methods (GET, POST, PUT, PATCH, DELETE).
- **Middleware:** a function that runs on every request before your handler (logging, auth checks).
- **Idempotent:** doing it twice has the same result as doing it once.
- **Authentication vs authorisation:** *who are you?* vs *what are you allowed to do?*
- **Tenant scoping:** each user's data is walled off so they only ever see their own.
- **IDOR:** a bug where changing an ID in a URL lets you access someone else's data.
- **Session vs JWT:** two ways to remember a logged-in user. A session is stored on the server; a JWT is a signed token the client carries.
- **CSRF:** an attack where another site tricks your browser into sending a request to a site you're logged into.
- **Hashing (passwords):** a one-way scramble of a password so the real one is never stored.
- **Rate limiting:** capping how many requests a client can make in a time window.
- **SSRF:** tricking a server into making requests to places it shouldn't, such as your internal network. It matters here because PulseBoard fetches URLs that users supply.
- **DNS rebinding:** a trick where a hostname first resolves to a safe address, then to an internal one.
- **HMAC signature:** a secret-based stamp on a message so the receiver can prove it really came from you.

## Real-time and infrastructure terms
- **Cache:** a fast copy of data kept nearby to avoid repeating slow work. **Cache-aside:** check the cache first, and on a miss load from the database and store it.
- **TTL (time to live):** how long a cached item is allowed to live before it expires.
- **Redis:** an in-memory data store, often used for caching and messaging.
- **Pub/Sub:** one part publishes messages to a channel; any parts subscribed receive them.
- **Polling:** repeatedly asking "anything new?" The alternative is being told when something changes.
- **SSE (Server-Sent Events):** the server pushes a one-way stream of updates to the browser over a long-lived HTTP connection.
- **WebSocket:** a two-way, long-lived connection between browser and server.
- **Heartbeat / ping-pong:** small periodic messages that prove a connection is still alive.
- **Backoff:** waiting longer between each retry.
- **Scheduler:** the part that decides what should run and when.
- **Job queue / worker:** a to-do list of jobs, and the processes that take jobs off it and do them.
- **Dead-letter queue:** where jobs that keep failing get parked for inspection.
- **At-least-once vs exactly-once:** guarantees on how many times a job runs. At-least-once may run twice, so jobs must tolerate repeats.
- **Jitter:** a small random offset so many tasks don't all fire at the same instant.
- **Flapping:** a service that keeps switching between up and down.
- **Webhook:** PulseBoard sending an HTTP request to a URL you choose when something happens.
- **CI (continuous integration):** automatic checks (lint, tests) that run on every change you push.
