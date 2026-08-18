---
name: Project and Service Development
description: >
  Governs the design, implementation, modification, review, testing, debugging,
  and delivery of application and service code, including architecture,
  databases, APIs, Docker, security, diagnostics, and development communication.
---

# Project and Service Development

## 1. Purpose

Use this skill as the primary engineering standard when designing, writing, modifying, reviewing, testing, or debugging application and service code.

The goal is to produce maintainable, secure, modular, testable, and operationally diagnosable software while respecting project boundaries, existing architecture, and explicit user decisions.

## 2. Activation

Apply this skill when the task involves one or more of the following:

- application or service development;
- implementation or modification of source code;
- code review or refactoring;
- software architecture or project structure;
- API design or response schemas;
- database models, queries, indexes, or migrations;
- Dockerfiles, Docker Compose, containerized development, or container execution;
- automated tests and regression checks;
- debugging through application or container logs;
- security review related to changed or newly introduced code;
- technical clarification with the user during development.

For narrow changes, apply the rules proportionally to the affected code and directly related execution paths. For greenfield projects, large refactors, or broad task pools, apply the full workflow and project-wide checks.

## 3. Rule Priority

When rules compete, use the following priority order:

1. Protect user data, credentials, and system integrity.
2. Respect explicit user instructions, permissions, and workspace boundaries.
3. Preserve compatibility with the existing project unless a change is explicitly approved.
4. Follow the established architecture, conventions, and contracts of the project.
5. Apply the engineering standards defined by this skill.
6. Prefer simpler, more maintainable, and more efficient solutions when they do not violate higher-priority constraints.

Never silently trade data safety, security, compatibility, or user-approved behavior for implementation convenience.

## 4. Development Workflow

### 4.1 Understand the Task

Before implementation:

- inspect the relevant project structure, configuration, dependencies, and existing conventions;
- identify the affected components, data flows, APIs, database objects, and runtime services;
- determine whether the task is local, cross-cutting, or architectural;
- identify any migration, compatibility, security, or deployment risk;
- verify current framework, library, and platform behavior against official documentation when the task depends on version-specific behavior.

If official documentation cannot be checked, do not claim that it was verified.

### 4.2 Resolve Material Ambiguity

Ask the user before implementation when ambiguity materially affects:

- architecture;
- persistent data;
- security or authorization;
- public API behavior;
- compatibility;
- destructive operations;
- service boundaries;
- deployment behavior;
- a significant product or UX decision.

For non-material ambiguity, prefer the least surprising solution that matches the existing project and clearly state any assumption that affects the result.

For greenfield or architectural work, perform sufficient requirements discovery before committing to a design.

### 4.3 Plan the Change

Before editing, determine:

- which files or modules should change;
- whether new modules are required;
- whether a database migration is required;
- what tests must be added or updated;
- what security checks apply;
- what Docker services must be rebuilt or restarted;
- what logs or runtime signals should be inspected after implementation.

Avoid unrelated refactors unless they are necessary for correctness, security, or maintainability of the requested change.

### 4.4 Implement

Implement the smallest coherent solution that satisfies the requested behavior while preserving project consistency.

Do not leave temporary workarounds, dead code, debug artifacts, or unexplained compatibility hacks unless the user explicitly asks for a temporary solution.

### 4.5 Verify

After implementation:

- run the relevant automated tests inside Docker;
- inspect affected service logs;
- test expected behavior;
- test meaningful negative and failure scenarios;
- verify database migrations and query behavior when applicable;
- perform the required scope of security review;
- check that no new warnings, crashes, or obvious regressions were introduced.

### 4.6 Handoff

When the task is complete, report:

- what changed;
- important implementation decisions;
- tests and checks performed;
- security findings and fixes, if applicable;
- migrations or operational actions required;
- known limitations or unresolved risks;
- a concise manual verification checklist before production deployment.

Do not claim a test, audit, documentation check, or runtime verification was completed unless it was actually performed.

## 5. Architecture and Code Standards

### 5.1 Modularity

Do not create monolithic catch-all files containing unrelated responsibilities.

Split code into logical modules when responsibilities diverge, navigation becomes difficult, testing becomes harder, or a file begins accumulating unrelated application layers or domains.

Prefer:

- clear ownership of responsibilities;
- small cohesive modules;
- explicit interfaces between layers;
- reusable components only where reuse is real;
- predictable file placement.

Do not split code mechanically when doing so would only add indirection without improving cohesion or maintainability.

### 5.2 Project Structure

Maintain a consistent and predictable directory structure regardless of programming language.

Keep application layers and responsibilities clearly separated according to the architecture used by the project, such as:

- transport or API layer;
- application or service layer;
- domain logic;
- persistence layer;
- integrations;
- configuration;
- tests;
- infrastructure.

Do not introduce a second competing organizational pattern into an established project without a clear reason and user approval when the change is architectural.

### 5.3 API Contracts

Keep API responses and data schemas consistent across the service.

Use the type system, schema system, or validation facilities provided by the language and framework.

For existing projects:

- preserve established response contracts unless a breaking change is explicitly requested;
- reuse existing error and success schemas;
- keep status codes, validation behavior, and serialization consistent.

For new services:

- define predictable success and error contracts;
- type or validate external payloads;
- avoid leaking internal implementation details through responses.

Protocol-specific conventions take precedence when a generic response envelope would be inappropriate.

### 5.4 Code Comments

Comments inside source-code files must be written exclusively in English.

Use comments only for visual separation of logical categories, blocks, or sections inside source-code files.

Do not use inline explanatory comments to describe business rules, implementation nuances, compatibility decisions, or non-obvious behavior. Such context should be documented through the project's dedicated documentation mechanisms, such as README.md, AGENT.md, or other applicable project documentation.

### 5.5 Modern and Supported Dependencies

Use current stable and supported versions of frameworks, libraries, modules, runtimes, and APIs unless the existing project requires a specific compatible version.

Before introducing or changing version-sensitive code:

- check official documentation;
- avoid deprecated APIs;
- consider known security issues;
- respect the project's dependency and runtime constraints.

Do not perform major dependency upgrades unrelated to the requested task without user approval.

### 5.6 Better Alternatives

If a meaningfully simpler, safer, faster, more modern, or more maintainable solution exists, prefer it when doing so does not violate higher-priority constraints.

If the alternative would materially change architecture, scope, compatibility, public behavior, data design, or another significant project decision:

1. explain the current approach and the proposed alternative;
2. explain why the alternative is preferable;
3. explain the main tradeoff;
4. recommend the preferred option;
5. ask for the user's decision before implementation.

If the improvement is local and does not materially change architecture, scope, compatibility, or user-visible behavior, apply the better solution without interrupting the task.

In the final handoff, briefly mention such a non-trivial implementation decision and explain why it was preferable to the most relevant alternative.

Do not interrupt implementation for trivial stylistic alternatives.

## 6. Security and Data Architecture

### 6.1 Secure by Default

Design and implement code defensively against relevant classes of vulnerabilities, including:

- SQL injection;
- cross-site scripting (XSS);
- cross-site request forgery (CSRF);
- insecure direct object references (IDOR);
- broken authentication or authorization;
- unsafe input handling;
- unintended data disclosure;
- secret leakage;
- insecure file or path handling;
- privilege escalation within the application or container.

Apply defenses appropriate to the actual stack and trust boundaries rather than adding irrelevant controls mechanically.

### 6.2 Authorization

Authorization must be enforced server-side at the correct resource or action boundary.

Do not rely on:

- hidden UI controls;
- client-provided ownership claims;
- predictable identifiers;
- frontend validation;
- route obscurity.

When access depends on ownership, role, tenant, organization, or another scope, validate that scope before returning or mutating the resource.

### 6.3 Database Changes

All persistent database schema changes must use the project's migration mechanism.

Do not modify an existing database schema solely to satisfy generic optimization rules. Schema refactoring, new indexes, normalization changes, denormalization, constraint changes, or other structural optimizations must be justified by actual project requirements, observed query patterns, maintainability problems, or clearly anticipated scale.

For MVP, local-development, staging, or other non-production databases, broader schema refactoring is allowed when it materially improves the project before release. Prefer fixing weak data architecture before it becomes a production compatibility burden.

When existing data must be preserved, perform the refactor through safe migrations.

A destructive reset or recreation of a non-production database is allowed only when the data is confirmed to be disposable or the project explicitly treats that database as disposable.

Do not directly drop, recreate, or destructively rewrite production-relevant tables when doing so risks data loss.

For destructive or irreversible operations:

- identify the risk explicitly;
- distinguish production-relevant data from disposable development data;
- prefer staged or backward-compatible migration strategies when data must be preserved;
- require explicit user approval before destructive operations when data loss could matter.

### 6.4 Database Query Quality

Avoid N+1 query patterns.

For frequently searched, filtered, joined, or sorted fields:

- evaluate appropriate indexes;
- consider selectivity and write cost;
- avoid speculative indexes that are not justified by real query patterns;
- inspect query plans for performance-sensitive paths when practical.

Keep queries bounded and predictable when operating on potentially large datasets.

### 6.5 Structured Logging and Timestamp Formats

Application logs must be clean, readable, and usable for automated diagnosis.

Use timestamp formats according to the target audience:

- machine-oriented timestamps used in structured logs, internal events, audit events, integrations, observability pipelines, or application processes must use ISO 8601 / RFC 3339 with an explicit timezone, for example: `2026-08-17T23:41:00+03:00`;
- human-facing timestamps used in public reports, CLI summaries, readable status output, or other places primarily intended for direct human reading must use: `HH:MM:SS DD.MM.YYYY`.

Do not duplicate the same timestamp in both formats inside a single structured event unless the project has an explicit requirement for both.

Logs should provide enough context to diagnose failures without exposing secrets, credentials, tokens, or sensitive user data.

Prefer structured fields for important operational context when supported by the stack.

Detailed internal errors and stack traces belong in logs, not in user-facing responses.

### 6.6 Error Handling

Handle application errors through a centralized and consistent mechanism where supported by the architecture.

User-facing errors must:

- be understandable;
- avoid internal stack traces;
- avoid secrets and implementation details;
- use the project's established response contract.

Internal logs should retain sufficient diagnostic information for root-cause analysis.

## 7. Security Review Scope

### 7.1 Greenfield or Broad Changes

For a new project, major refactor, architectural change, or broad task pool, perform an express security review across the relevant system surface.

Review at least the applicable areas:

- authentication;
- authorization;
- input validation;
- database access;
- data exposure;
- secret handling;
- external integrations;
- file and path operations;
- dependency risks;
- container privileges;
- error handling;
- logging;
- destructive operations.

### 7.2 Focused Changes

For a focused feature or narrow update, review:

- changed code;
- directly related call paths;
- affected authorization and data boundaries;
- directly connected persistence or integration logic;
- new or changed external inputs.

Do not expand a focused task into an unrelated project-wide security rewrite unless a critical issue requires escalation.

### 7.3 Security Review Report

Report:

- what was checked;
- what vulnerabilities or risks were found;
- how each finding was fixed or mitigated;
- what remains unresolved, if anything.

Do not invent findings to make the report appear complete.

## 8. Docker and Environment Isolation

### 8.1 Docker-First Execution

Run project services and automated tests through Docker containers.

Do not install project dependencies directly into the host system unless the user explicitly authorizes it.

Prefer the project's existing Docker, Docker Compose, or equivalent container orchestration configuration.

### 8.2 Non-Root Containers

Application containers must not run application processes as `root`.

Dockerfiles and container configuration must explicitly use a dedicated unprivileged user.

When practical and compatible with the project, name the container user after the service.

Ensure required files and runtime directories have the correct ownership and permissions for that user.

### 8.3 Workspace Boundary

Operate only inside the assigned project working directory unless an exception applies.

Before reading from, writing to, creating, deleting, or modifying resources outside the assigned service directory, obtain explicit user permission unless access is covered by a previously established permanent project rule.

One-time permission does not become permanent permission.

Installing packages into the host system also requires explicit user approval.

### 8.4 Agent Skills Directory Exception

The directory containing the core agent instructions (`agent-skills`) may always be read, even when it is outside the current project working directory.

Writing to `agent-skills` is allowed only when the user explicitly requests that specific change at the time it is needed.

Permission to write to `agent-skills`:

- is never implied;
- does not persist automatically;
- does not authorize unrelated skill changes.

### 8.5 Cleanup

Remove temporary files, local caches, and unused project-scoped containers or images when they are no longer needed and removal is safe.

Do not delete global host resources, shared Docker assets, or files outside the project without explicit user permission.

Never perform cleanup that could remove user data, shared dependencies, or resources belonging to another project.

## 9. Testing and Diagnostics

### 9.1 Autonomous Log Analysis

When an error occurs, inspect relevant container logs or project-local log files directly when access is available.

Use logs to identify the root cause before asking the user to manually copy diagnostic output that the environment already exposes.

Do not repeatedly ask the user for information that can be obtained from the available project environment.

### 9.2 Targeted Automated Tests

Add or update unit and integration tests for newly introduced or changed logic.

Run relevant tests inside Docker.

Tests should cover:

- expected behavior;
- meaningful edge cases;
- regression risks introduced by the change;
- relevant authorization or validation paths;
- important failure scenarios.

Do not create broad unrelated test suites solely to increase coverage numbers.

### 9.3 Pre-Handoff Audit

Before declaring the task complete:

- inspect relevant service logs;
- exercise realistic negative scenarios;
- confirm expected error behavior;
- check for unexpected warnings or crashes;
- verify that the changed path works in the containerized environment.

If a required check cannot be performed, state that limitation explicitly.

### 9.4 Manual Verification Checklist

Provide the user with a concise manual checklist for production-readiness verification when the task changes deployable application behavior.

The checklist should focus on user-visible behavior, deployment-sensitive paths, permissions, persistence, integrations, and failure handling relevant to the actual change.

## 10. Communication

### 10.1 Requirements Discovery

For greenfield services, architectural work, or underspecified features, ask targeted questions to understand:

- the intended user flow;
- business rules;
- permissions and roles;
- data lifecycle;
- integrations;
- expected scale;
- failure behavior;
- compatibility constraints;
- deployment expectations.

Do not conduct unnecessary interviews for small, well-defined implementation tasks.

### 10.2 Clarification Before Risky Decisions

Clarify uncertainty before implementation when a wrong assumption could cause:

- data loss;
- a security issue;
- a breaking API change;
- incompatible architecture;
- incorrect permissions;
- destructive infrastructure changes;
- materially different product behavior.

### 10.3 Engineering Communication

Keep development communication concise and decision-oriented.

When presenting a technical choice, state:

- the recommended option;
- why it is preferred;
- the main tradeoff;
- whether user approval is required before proceeding.

Do not overload the user with low-value implementation narration.

## 11. Definition of Done

A development task is complete only when all applicable conditions are satisfied:

- the requested behavior is implemented;
- the implementation follows the project's architecture and conventions;
- code is modular and maintainable;
- relevant security controls are present;
- persistent database changes use migrations, except explicitly approved disposable non-production resets;
- Docker execution remains functional;
- application processes do not require root inside containers;
- relevant automated tests pass;
- affected logs have been inspected;
- meaningful failure scenarios have been checked;
- the required scope of security review has been performed;
- temporary debug artifacts have been removed;
- required operational or migration steps are documented;
- relevant non-trivial implementation choices are briefly explained in the final handoff when a better approach was selected automatically;
- the user receives a manual verification checklist when production behavior changed.

If any applicable condition cannot be verified, clearly mark it as unverified rather than treating the task as fully validated.
