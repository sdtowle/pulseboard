# PulseBoard: Project Brief

Why this project exists, what it is for, and what "done" means. For the technical blueprint see [AGENTS.MD](../AGENTS.MD); for the work itself see [backlog.md](backlog.md).

## Who this is for

A front-end developer with about 8 years of experience (6 in Shopify development). Strong in HTML, CSS (Sass, Tailwind), vanilla JavaScript, React and Preact; some Next.js and Remix. Back-end experience is limited to small Express proxy apps and scheduled jobs. SQL experience is light and mostly AI-assisted.

## Why this project exists

- Broaden from front-end development into full-stack development.
- Learn software engineering principles by building a real system end to end, not by following tutorials.
- Build something substantial and well documented, in the open.

PulseBoard (a service health and uptime monitor) was chosen because it needs every layer: a database, an API, background workers, real-time updates and a UI.

## What I want to learn

- Back-end architecture, data modelling and SQL, and how they fit with a front end.
- Project structure, naming conventions and terminology used on professional teams.
- Writing down decisions and trade-offs (ADRs).
- Testing, CI and deployment.
- How the underlying mechanisms work (HTTP, routing, state), by rebuilding selected parts without a framework once the app works. See "Working agreement".

## Definition of done

A feature is done when it is **working, tested, merged to `main`, and deployed**.

## Constraints

- **No deadline.** This is self-development.
- **Time is limited and irregular.** Work is broken into small tickets that can be finished in short sessions and picked up again after gaps.
- **One language at a time.** Node.js and TypeScript are used so the learning goes into engineering principles, not a new language. Other languages may be explored once the app is stable.
- **Public repository.** Docs, ADRs and tests are part of what gets read.

## Working agreement (human and AI)

- All application logic, schemas and tests are written by hand by the developer.
- AI acts as mentor and reviewer: it asks guiding questions, points out edge cases and checks standards, and does not write solution code.
- **Exception: project setup.** AI may create initial scaffolding (folder structure, dependency lists, scripts and configuration) so work can start from a known baseline. It explains the conventions and the reasoning behind each choice so they can be learned, not just used.
- Documentation (ADRs, glossary, this brief) can be drafted by AI on request. The developer reviews and edits it.
- New terms go into [glossary.md](glossary.md) as they come up.
- **Frameworks:** the intent is to learn how things work underneath. Whether the back end uses a framework, and whether selected parts are rebuilt without one after the app is stable, is still being decided. See ADR 0004.

## Open decisions

- Back-end framework (Express or none), see ADR 0004.
- Database and how it is accessed (ADR not yet written; ticket 0.2).
- Front-end approach.
- Which tickets form the MVP and which are stretch goals.
