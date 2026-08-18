---
name: Project Documentation and Agent Memory
description: >
  Governs the creation, structure, maintenance, synchronization, and verification
  of README.md, AGENT.md, and related project documentation, including public
  project guidance, architecture context, development status, API references,
  RoadMap state, and long-term agent memory.
---

# Project Documentation and Agent Memory

## 1. Purpose

Use this skill as the primary standard for creating, updating, reviewing, and synchronizing `README.md`, `AGENT.md`, and related project documentation.

The documentation system has two distinct roles:

- `README.md` is the public, human-oriented project guide and operational reference;
- `AGENT.md` is the compact, project-specific long-term memory for the agent.

The goal is to keep project documentation accurate, useful, synchronized with the implementation, compact where appropriate, and free from duplicated or speculative information.

## 2. Activation

Apply this skill when one or more of the following occurs:

- `README.md` is created or updated;
- `AGENT.md` is created or updated;
- project structure changes;
- service boundaries or component relationships change;
- public API endpoints change;
- startup, Docker, environment, migration, testing, or diagnostic commands change;
- the technology stack changes;
- RoadMap status changes;
- architecture or internal implementation state changes;
- a significant implementation decision is finalized;
- a development task is completed and documentation impact must be checked.

After every completed development task, perform a documentation impact check even when no documentation update is ultimately required.

## 3. Documentation Responsibilities

### 3.1 README.md

`README.md` must answer:

- what the project is;
- what problem it solves;
- how the system is structured;
- what components exist;
- how the project is configured;
- how the project is started;
- how the project is tested;
- how the project is diagnosed;
- what public API surface exists;
- what work is still planned or partially implemented.

`README.md` must not be used as internal agent memory.

### 3.2 AGENT.md

`AGENT.md` must answer:

- how the project is currently structured internally;
- how services and components interact;
- what project-specific technical constraints must not be violated;
- what important implementation decisions are already established;
- what is implemented, partially implemented, decided, or still planned;
- where the most important project-specific responsibilities live;
- what context allows the agent to continue work without rereading the entire codebase.

`AGENT.md` must not become a duplicate of `README.md`, a changelog, or a repository of generic coding rules.

## 4. README.md Language and Style

`README.md` must be written in Russian.

Use:

- strict technical language;
- concise explanations;
- consistent Markdown structure;
- stable section naming;
- executable commands;
- project-specific terminology.

Avoid:

- conversational introductions;
- slang;
- promotional wording;
- vague statements;
- decorative prose;
- duplicated explanations.

## 5. README.md Standard Design

Use the following Architecture First structure as the default project-wide README standard.

### 5.1 Standard Section Priority

Use this section order when applicable:

1. project title;
2. short project description;
3. table of contents;
4. system overview;
5. architecture;
6. components;
7. full project tree;
8. technology stack;
9. configuration;
10. startup and operation;
11. API;
12. database and migrations when applicable;
13. testing;
14. operations and diagnostics;
15. RoadMap.

Do not add empty sections only to satisfy the template.

If the project has a strong domain-specific reason to add another section, place it where it logically belongs without breaking the overall hierarchy.

### 5.2 Project Title

Start with:

```md
# Project Name
```

Use the actual project or repository name.

### 5.3 Short Description

Immediately below the title, provide a concise technical description of the project in approximately 1-3 sentences.

The description should identify:

- the system purpose;
- the primary user or integration surface;
- the main responsibility of the project.

### 5.4 Table of Contents

Provide a table of contents for the main README sections.

Keep headings stable so generated anchors remain predictable.

Do not include every deeply nested subsection unless the README is large enough to justify it.

### 5.5 System Overview

Use:

```md
## Обзор системы
```

Include:

- purpose;
- primary capabilities;
- the main system flow.

A compact text diagram may be used when it improves understanding.

Example:

```text
User
  ↓
Frontend / Telegram Bot
  ↓
Backend API
  ↓
Database / External Services
```

### 5.6 Architecture

Use:

```md
## Архитектура
```

Describe:

- architectural model;
- major service relationships;
- communication directions;
- data ownership;
- important infrastructure boundaries.

Use compact diagrams when they reduce explanation cost.

### 5.7 Components

Use:

```md
## Компоненты
```

Describe the responsibility of each significant component.

Do not describe a component with circular statements such as "the backend contains backend code."

Describe actual ownership and responsibility.

### 5.8 Full Project Tree

Use:

```md
## Структура проекта
```

Include the full project directory tree as a project memory aid for both humans and agents.

The tree should reflect the current repository structure.

Include project files and directories that exist in the repository.

Generated external dependency trees such as `node_modules` or other non-repository dependency contents do not need to be expanded.

After the tree, describe the responsibility of directories and important files when their purpose is not obvious.

Keep the tree synchronized when files or directories are added, moved, renamed, or removed.

### 5.9 Technology Stack

Use:

```md
## Технологический стек
```

Group technologies by purpose.

Recommended categories when applicable:

- Backend;
- Frontend;
- Database;
- Cache / Queue;
- Infrastructure;
- Testing;
- Observability;
- External Integrations.

List the actual project stack.

Do not reproduce the complete dependency lockfile or every transitive package.

### 5.10 Configuration

Use:

```md
## Конфигурация
```

Document:

- how `.env` is prepared;
- which configuration files are relevant;
- which variables or groups of variables are required for startup;
- where the full configuration template is located.

Do not publish real secret values.

When detailed environment variable semantics are already maintained in `.env.example` and validation schemas, reference them rather than duplicating the same rules extensively.

### 5.11 Startup and Operation

Use:

```md
## Запуск
```

Provide a complete working startup path from a clean project checkout.

The instructions must include all required steps that are actually necessary, such as:

- preparing configuration;
- building containers;
- starting services;
- applying migrations;
- seeding data when required;
- checking service state.

Commands must match the current project.

Do not document commands that have not been verified when verification is available.

### 5.12 API

Use:

```md
## API
```

README must contain a concise catalog of public endpoints grouped by functional area.

For each endpoint, include at minimum:

- HTTP method;
- path;
- concise purpose.

Example:

```md
### Authentication

`POST /api/auth/login`

Авторизация пользователя.
```

When OpenAPI, Swagger, or another generated API documentation interface is available:

- reference it as the detailed API source of truth;
- keep README endpoint descriptions concise;
- do not manually duplicate full request and response schemas unless the project has a specific reason.

When generated API documentation is not available, README may include more detailed endpoint information.

### 5.13 Database and Migrations

When the project uses a database, document:

- migration commands;
- initialization requirements;
- relevant development or operational database actions;
- any required ordering with application startup.

Do not include credentials.

### 5.14 Testing

Use:

```md
## Тестирование
```

Document the actual commands required to run relevant project tests.

Prefer Docker-based commands when the project follows Docker-first development.

### 5.15 Operations and Diagnostics

Use:

```md
## Эксплуатация и диагностика
```

Document practical operational commands, such as:

- service status;
- container logs;
- restart commands;
- health checks;
- other project-specific diagnostic actions.

Commands must remain executable and current.

### 5.16 RoadMap

Use:

```md
## RoadMap
```

Use the following states:

```md
### Planned
### Foundation
### In Progress
```

Meaning:

- `Planned` — work is intended but implementation has not started;
- `Foundation` — part of the required technical foundation exists, but the feature is not actively complete;
- `In Progress` — implementation is actively underway.

When a task or feature is fully completed, remove it from `RoadMap`.

Do not use RoadMap as a completed-task history.

## 6. AGENT.md Language and Style

`AGENT.md` must be written exclusively in English.

Use high-density technical language optimized for agent context efficiency.

Prefer:

- short bullets;
- compact factual statements;
- stable terminology;
- explicit state labels;
- direct architecture references.

Avoid:

- introductions;
- narrative history;
- repeated README content;
- generic engineering guidance;
- explanations that do not reduce future code-reading effort.

Every retained line should help a future agent understand the project faster or avoid a meaningful mistake.

## 7. AGENT.md Standard Structure

Use the following structure when applicable:

```md
# Agent Context

## Architecture
## Service Relationships
## Critical Constraints
## Current Implementation State
## Active Work / Known Gaps
## Key Decisions
## Documentation References
```

Do not create empty sections solely for consistency.

### 7.1 Architecture

Store the current project-specific architectural shape.

Example:

```md
- Backend owns subscription lifecycle and authorization decisions.
- Telegram bot and frontend consume the backend API.
```

### 7.2 Service Relationships

Store important inter-service communication and data ownership.

Example:

```md
- API and Telegram bot share PostgreSQL-backed subscription state.
- Frontend never communicates with VPN nodes directly.
```

### 7.3 Critical Constraints

Store only project-specific constraints.

Examples:

```md
- Subscription activation is performed only by the backend service.
- VPN node configuration is immutable from the frontend.
```

Do not store generic skill rules such as:

```md
- Always use Docker.
- Use database migrations.
- Write comments in English.
```

Generic engineering rules belong in the shared skills, not in project memory.

### 7.4 Current Implementation State

Describe what the project currently does.

Prefer current-state summaries over task history.

Bad:

```md
- Redis was added last week.
- Authentication was later changed.
```

Good:

```md
Authentication:
- JWT access tokens.
- Redis-backed refresh sessions.
- Refresh rotation enabled.
```

### 7.5 Active Work / Known Gaps

Store incomplete or intentionally missing project-specific work that affects future tasks.

Do not keep obsolete gaps after they are resolved.

### 7.6 Key Decisions

Store finalized project-specific decisions that materially affect future implementation.

Use a compact format:

```md
- Decision: Refresh sessions use Redis instead of PostgreSQL.
  Reason: Session state is short-lived and does not belong to persistent domain storage.
```

Store the reason only when it helps prevent future re-evaluation or architectural drift.

Do not preserve the entire discussion that led to the decision.

### 7.7 Documentation References

Reference `README.md` or other project documentation for commands and public guidance instead of duplicating them.

Example:

```md
- Startup and operational commands: see README.md.
```

## 8. State Accuracy

`AGENT.md` must distinguish implementation state accurately.

Use explicit state semantics when needed:

- `Implemented` — exists and is verified in the project;
- `Decided` — decision is finalized but implementation may not exist yet;
- `Planned` — intended but not implemented;
- `Partial` — foundation or incomplete implementation exists;
- `Blocked` — cannot proceed because of a known dependency or constraint.

Do not record discussed ideas as implemented facts.

Do not record tentative options as finalized decisions.

When uncertain, inspect the relevant implementation before updating project memory.

## 9. Current State Over History

`AGENT.md` describes the current project state, not the chronological history of development.

When implementation changes:

- replace outdated state;
- remove obsolete facts;
- update architectural relationships;
- retain historical context only when it explains a current constraint or prevents a likely regression.

Do not accumulate completed-task summaries indefinitely.

If a completed task changes the current architecture, record the resulting architecture rather than the fact that the task was completed.

## 10. No Duplication of Generic Rules

Do not duplicate shared skill instructions inside project documentation.

Examples of content that should remain in shared skills:

- Docker-first engineering rules;
- comment language rules;
- database migration standards;
- generic security rules;
- generic environment-management rules;
- generic testing policy.

Project documentation should contain only project-specific facts, usage, structure, configuration, and decisions.

## 11. Secret Protection

Never place real secrets in:

- `README.md`;
- `AGENT.md`;
- RoadMap entries;
- code examples;
- command examples;
- architecture notes.

Do not include:

- passwords;
- API keys;
- access tokens;
- refresh tokens;
- bot tokens;
- JWT secrets;
- private keys;
- production credentials.

Reference variable names instead.

Good:

```md
Requires `TELEGRAM_BOT_TOKEN`.
```

Forbidden:

```md
TELEGRAM_BOT_TOKEN=actual-secret-value
```

## 12. Documentation Synchronization Workflow

After every completed development task, perform the following two-stage documentation impact check.

### 12.1 README.md Check

Update `README.md` when one or more of the following changed:

- project structure;
- significant files or directories;
- public endpoints;
- startup commands;
- Docker configuration;
- environment preparation;
- migrations;
- service list;
- technology stack;
- testing commands;
- diagnostic commands;
- RoadMap state;
- public project behavior that requires documentation.

If none changed, do not rewrite README unnecessarily.

### 12.2 AGENT.md Check

Update `AGENT.md` when one or more of the following changed:

- architecture;
- service relationships;
- component responsibilities;
- internal implementation state;
- project-specific constraints;
- important finalized technical decisions;
- active work;
- known gaps;
- internal ownership of data or behavior.

If none changed, do not rewrite AGENT.md unnecessarily.

### 12.3 Token-Efficient Updates

Update only affected sections whenever practical.

Do not regenerate entire documentation files when a focused edit is sufficient.

Preserve stable wording and section order when the content remains valid.

This minimizes documentation drift and unnecessary agent context usage.

## 13. Documentation Is Part of the Task

If implementation changes require documentation changes, the documentation update is part of the same development task.

Do not declare the task complete while required documentation is stale.

Examples:

- a new public endpoint requires README API update;
- a moved directory requires README tree update;
- a new service relationship requires AGENT.md update;
- a RoadMap item becoming active requires RoadMap state update;
- a completed RoadMap feature must be removed;
- a changed startup command requires README update.

## 14. Verification of Documentation

### 14.1 Command Verification

README commands must be complete and operational.

When the environment allows verification, run or otherwise validate commands affected by the task.

Pay particular attention after changes to:

- Docker Compose;
- `.env`;
- migrations;
- service names;
- startup flow;
- test commands;
- diagnostic commands.

Do not knowingly document stale or broken commands.

If verification cannot be performed, mark the relevant instruction as unverified in the task handoff rather than presenting it as confirmed.

### 14.2 Project Tree Verification

Before updating the README project tree, inspect the current repository structure.

Do not rely on memory when the filesystem is available.

### 14.3 API Verification

When documenting endpoints:

- inspect the actual routing or generated API documentation;
- do not invent paths, methods, or behavior;
- use OpenAPI / Swagger as the detailed source of truth when available.

### 14.4 AGENT.md Verification

Before recording architecture or implementation state, verify that the statement reflects the current project.

Do not preserve speculative or outdated facts.

## 15. Information Density

### 15.1 README.md

README may be detailed because it serves both humans and agents as a broad project reference.

Prefer complete, structured information over excessive compression.

### 15.2 AGENT.md

AGENT.md must remain compact.

Do not document obvious facts.

Bad:

```md
- The backend contains backend code.
- PostgreSQL stores data.
- Docker runs containers.
```

Good:

```md
- `backend/services/subscriptions.py` owns subscription lifecycle transitions.
- API and Telegram bot share subscription state through PostgreSQL.
```

Every retained line should justify its context cost.

## 16. Definition of Done

A documentation-related task is complete only when all applicable conditions are satisfied:

- README impact has been checked;
- AGENT.md impact has been checked;
- only affected sections were updated when a focused edit was sufficient;
- README remains in Russian;
- AGENT.md remains in English;
- README follows the standard Architecture First design;
- README project tree reflects the current repository structure;
- technology stack reflects the actual project;
- startup instructions are complete and current;
- API catalog reflects the actual public API surface;
- generated API documentation is referenced when available;
- database and migration instructions are current when applicable;
- testing and diagnostic commands are current;
- RoadMap uses the defined states;
- completed RoadMap items have been removed;
- AGENT.md reflects current architecture rather than history;
- AGENT.md distinguishes implemented, decided, partial, planned, and blocked state accurately when needed;
- AGENT.md contains project-specific context rather than generic skill rules;
- obsolete or speculative facts have been removed;
- README and AGENT.md do not unnecessarily duplicate each other;
- no secrets or sensitive credentials are present;
- affected commands were verified when verification was available.

If any applicable documentation check cannot be performed, mark it explicitly as unverified rather than treating the documentation as fully validated.
