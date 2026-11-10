# ADR 0009: Use PostgreSQL as the database

- **Status:** Proposed. To be confirmed after the first schema is built (ticket 1.1).
- **Date:** 2026-11-10

## Context

PulseBoard stores two kinds of data:

- **A small relational core:** users own monitors, and monitors own check results.
- **A large, growing stream of check results:** one row per check, added constantly and never edited, then summarised and eventually pruned.

The planned workload is about 20 monitors checked once a minute at first, growing to around 1,000. That is roughly 29,000 results a day at the start and 1.4 million a day at the larger size (see the estimates in the project discussion and ticket 1.6 on retention).

Two separate processes, the API and the worker, both need to read and write the data.

The developer has limited experience with relational databases and wants to learn them by building. This decision is made on broad grounds and will be checked against real use.

The options considered:

- **PostgreSQL:** a relational database that runs as a separate server. Widely used, with a large community and plenty of learning material.
- **SQLite:** a relational database that is a file read directly by the application, with no server. Very simple to set up. Writes by several processes at once are more limited.
- **MySQL / MariaDB:** a relational database similar in kind to PostgreSQL.
- **MongoDB:** a document database that stores JSON-like records. It is more flexible about shape and gives up some of the guarantees between related records.
- **TimescaleDB:** an extension that runs on top of PostgreSQL and adds features for large amounts of timestamped data. It is not a separate database.
- **ClickHouse / InfluxDB:** databases built for analytics and time-series data. They are more than the first version needs.

## Decision

Use **PostgreSQL**.

## Rationale

- **Relational integrity.** Users, monitors and results reference each other. A relational database can enforce those links and other rules itself, so bad data cannot get in.
- **Concurrent access.** The API and the worker are separate processes that read and write at the same time. PostgreSQL is built for many clients using it at once.
- **Room to grow.** The expected load is modest, but PostgreSQL leaves headroom to grow without changing databases.
- **Learning value and reach.** It is widely used, well documented, and is the database underneath services the developer has already used (such as Supabase).
- **Keeps options open.** TimescaleDB can be added later as an extension if the results table outgrows plain PostgreSQL. It is not needed for now.

## Consequences

- PostgreSQL runs as a separate server, so local development needs it started (likely in a container, ticket 0.3), and production needs a managed service or a server to look after.
- Things to keep an eye on as the data grows:
  - **Disk use and cleanup.** Deleting old results leaves dead rows until vacuum reclaims the space. Splitting the results table by time (partitioning) is one way to drop old data cheaply.
  - **Connections.** Each connection uses memory, and both the API and the worker will hold some. Connection pooling may be needed.
  - **Memory settings** on the database server, which should be reviewed when the load increases.
- This decision does not yet cover how the code talks to the database (raw SQL, a query builder or an ORM), which driver to use, or how schema changes are managed. Each needs its own decision.
- Where the database code lives in the server is still to be revisited (see ADR 0007).

## Revisit

Confirm or change this decision after the first schema is built (ticket 1.1), when the developer has worked with the database directly. If the results table grows enough to cause problems, evaluate TimescaleDB. Any change of database should be recorded in a new ADR that supersedes this one.
