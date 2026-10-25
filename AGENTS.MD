PulseBoard — Full-Stack Development & Architecture Blueprint

Overview & Mission

PulseBoard is an end-to-end, real-time distributed service health and uptime monitoring system.

This repository and project serve as a structured, hands-on environment for personal software engineering growth. The goal is to build deep, language-agnostic engineering skills, strengthen system architecture instincts, and master full-stack development without relying on AI code generation or boilerplate automation.

Core Philosophy & Operating Rules

1. Active Learning over Passive Generation:

	⚬ All code, logic, and schema definitions are written by hand by the developer.

	⚬ AI acts strictly as a mentor, architecture reviewer, and static analyzer—asking guiding questions, identifying edge cases, and enforcing standards without writing or outputting solution code.

	⚬ Exception: initial project setup (folder structure, dependency lists, scripts, configuration) may be scaffolded by AI, with the conventions and reasoning explained so they can be learned. See docs/project-brief.md.

2. Vertical Slice Development:

	⚬ Features are built as end-to-end vertical slices (connecting database, server logic, API endpoints, and client-side UI) rather than isolated horizontal layers.

3. Production-Grade Engineering:

	⚬ Prioritize clean architecture, explicit data contracts, scalable time-series storage, robust error handling, and framework-free fundamentals (vanilla DOM, proxy state, raw protocol mechanics).

Tech Stack & Architecture Highlights

Layer	System Target	Key Focus Areas
Frontend UI	Zero-framework Reactive Web UI	Custom state management (Proxy), WebSockets/SSE live streams, lightweight time-series charts, DOM performance optimization.
Web API / Application Server	REST API & WebSocket Gateway	Modular route handling, custom authentication middleware, rate limiting, and input validation.
Background Engine	Scheduled Worker & Telemetry Engine	Asynchronous HTTP/TCP check execution, DNS lookup handling, network timeouts, and concurrency management.
Primary Database	Relational DB (e.g., PostgreSQL / SQLite)	ACID compliance, clean schema relationships, explicit constraints.
Time-Series / Caching Layer	In-Memory Cache (e.g., Redis)	High-frequency metric caching, real-time status Pub/Sub, database read load reduction.

Project Epics & Roadmap

⚬ [ ] Epic 1: Core Monitoring Engine (MVP)

	⚬ Schema definition for monitors & check results.

	⚬ Isolated HTTP ping worker with network error handling.

	⚬ Time-series log persistence.

	⚬ CLI execution harness.

⚬ [ ] Epic 02: Management API & UI

	⚬ CRUD endpoints for monitored endpoints.

	⚬ User authentication & tenant scoping.

	⚬ Dynamic dashboard UI for status inspection.

⚬ [ ] Epic 03: Caching, Pub/Sub & Real-Time Telemetry

	⚬ Redis cache integration for immediate status lookups.

	⚬ WebSocket / SSE integration for live dashboard updates.

	⚬ Background worker job queues and scheduler.

⚬ [ ] Epic 04: Incident Management & Alerting

	⚬ Flapping detection and incident grouping.

	⚬ Webhook/Email notification dispatch.

Repository Structure & Project Management

⚬ Project Tracking: Managed via GitHub Projects (Kanban: Backlog, Ready / In Sprint, In Progress, Done).

⚬ Issue Format: User stories with explicit Acceptance Criteria (AC) and MoSCoW prioritization.

⚬ Documentation: Architectural Decision Records (ADRs) and data schemas kept up-to-date in /docs.
