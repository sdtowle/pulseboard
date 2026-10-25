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

## Decision-making terms
- **Back-of-the-envelope estimation:** rough arithmetic to find the order of magnitude of load, storage and bandwidth (10 writes a second or 10,000?). Also called capacity estimation or napkin math.
- **Workload characterization:** describing how a system will be used: read/write ratio, access patterns, peak vs average load, how fast data grows.
- **Functional vs non-functional requirement (NFR):** what the system *does* vs how well it must do it (speed, durability, cost, ease of running). Database choices are driven mostly by the non-functional ones.
- **Trade-off analysis:** comparing options by what each gains and gives up, instead of looking for a "best" one.
- **Decision matrix:** a table of candidates against criteria (optionally weighted) that makes the reasoning visible and open to challenge.
- **Proof of concept (PoC):** a small, throwaway build that tests a risky assumption. Similar to a spike.
- **Fitness for purpose:** how well an option suits *your* needs, rather than how good it is in general.
- **Reversibility:** how costly it is to change a decision later.
- **Order of magnitude:** a factor of ten. Estimates aim to get this right, not the exact number.
- **QPS / TPS:** queries or transactions per second.
- **Headroom:** spare capacity above the expected load.
- **Read/write ratio:** how many reads the system does for each write.
- **Working set:** the portion of the data in active use at a given time.

## Tooling terms
- **Runtime:** the program that executes your code (here, Node.js).
- **Linter:** a tool that scans code for likely mistakes and style problems without running it (ESLint).
- **Formatter:** a tool that rewrites code layout (spacing, line breaks) to one consistent style (Prettier).
- **Static typing:** types are checked before the code runs. TypeScript adds this to JavaScript.
- **Type erasure:** types exist only at compile time and are removed from the running program, so they can't check data arriving from outside.
- **Runtime validation:** checking at run time that outside data (request bodies, environment variables) has the expected shape. Schema libraries such as Zod do this.
- **Boundary (system boundary):** the point where data enters or leaves your code (HTTP requests, database, files). Validate here.

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
- **Relational database (SQL):** data in tables with defined columns, linked by keys and queried with SQL.
- **Non-relational (NoSQL):** a broad family that doesn't use linked tables. Includes document (JSON-like records), key-value (lookup by key) and wide-column stores.
- **Primary key / foreign key:** a column that uniquely identifies a row / a column that points to a row in another table.
- **Normalization / denormalization:** splitting data into separate tables to avoid repeating it / deliberately repeating some to make reads faster.
- **Transaction:** a group of changes that all succeed or all fail together.
- **ACID:** the guarantees a transaction gives: **Atomicity** (all or nothing), **Consistency** (rules always hold), **Isolation** (concurrent transactions don't interfere), **Durability** (once saved, it survives a crash).
- **Embedded vs client-server database:** one runs inside your own process and stores data in a file; the other runs as a separate server your app connects to.
- **Connection pooling:** reusing a small set of open database connections instead of opening one per request.
- **Query builder / ORM / raw SQL:** three ways to talk to a database from code: composing queries with functions / mapping rows to objects / writing the SQL yourself.
- **Query planner / `EXPLAIN`:** the part of a database that chooses how to run a query, and the command that shows its choice.
- **Multi-tenancy:** one running system serving many separate users, whose data must stay isolated.
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
