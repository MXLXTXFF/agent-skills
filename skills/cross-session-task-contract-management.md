---
name: Cross-Session Task Contract Management
description: >
  Governs local cross-session coordination between service-specific chat sessions,
  including *_SYNC.md naming, task contracts, service boundaries, read-only sibling
  service access, dependency handoff, contract verification, completion cleanup,
  and reverse synchronization when one service creates follow-up work for another.
---

# Cross-Session Task Contract Management

## 1. Purpose

Use this skill when development is split across separate chat sessions or agents by service responsibility and one service requires work from another service to complete or integrate a feature.

The goal is to coordinate cross-service work through compact local synchronization files without allowing one session to take over another service's codebase, duplicate project documentation, or lose track of pending inter-service dependencies.

Synchronization files are temporary operational contracts between service-specific sessions. They are not RoadMaps, changelogs, architecture documents, or long-term project memory.

## 2. Activation

Apply this skill when one or more of the following is true:

- the project is developed through separate service-specific chat sessions;
- the current task depends on functionality owned by another service;
- a session must create, read, update, or clean a `*_SYNC.md` file;
- a frontend task requires backend functionality;
- a backend task requires frontend follow-up work;
- another service must implement an API, schema, integration, configuration, or behavior required by the current task;
- the current session must inspect a sibling service to understand an inter-service contract;
- completion of a task creates a new dependency for another service.

## 3. Synchronization File Naming

### 3.1 General Naming Rule

Name synchronization files by the target service that must perform the pending work.

Use:

```text
<TARGET_SERVICE>_SYNC.md
```

Examples:

```text
FRONTEND_SYNC.md
BACKEND_SYNC.md
BOT_SYNC.md
WORKER_SYNC.md
INFRA_SYNC.md
```

Use uppercase service identifiers with `_SYNC.md`.

### 3.2 File Location

Store synchronization files in the project or workspace root where the participating sessions can access them.

Do not hide synchronization files inside one service implementation directory when multiple service sessions must read them.

### 3.3 Language

Synchronization files must be written exclusively in English.

Use concise technical language.

## 4. Local-Only Synchronization Files

`*_SYNC.md` files are local coordination artifacts.

They must not be committed or pushed to Git or GitHub.

Ensure synchronization files are excluded from repository tracking.

A suitable ignore rule may be:

```gitignore
*_SYNC.md
```

Do not rely on Git history as the synchronization transport for these files.

If a project is moved to another machine:

- the synchronization file may be copied together with the working project when pending tasks still matter;
- or it may be omitted when no pending synchronization work remains.

Do not preserve empty or obsolete synchronization history solely for migration purposes.

## 5. Standard File Header

Every synchronization file must contain a short fixed-purpose header explaining what the file is and how it is used.

Recommended format:

```md
# Backend Sync

Pending cross-session contracts targeting the backend service.

Tasks are created by related service sessions and removed by the target service only after full implementation and verification.

Operational rules: follow the `Cross-Session Task Contract Management` skill.
```

Adapt the title and target service name to the file.

Keep the header compact.

Do not add project architecture, generic instructions, status reports, or long explanatory text.

When no pending tasks exist, only the standard header remains.

## 6. Service Ownership Boundaries

### 6.1 Write Boundary

Each session may modify source code only inside the service it owns.

A frontend session must not implement backend code.

A backend session must not implement frontend code.

The same rule applies to any other service-specific session.

When work is required from another service, create a task in that target service's synchronization file instead of editing its implementation directly.

### 6.2 Read-Only Sibling Access

A session may read files from a directly related sibling service when necessary to understand or verify a cross-service dependency.

Read-only access may include:

- source code;
- routes;
- API contracts;
- schemas;
- configuration structure;
- tests;
- logs;
- `README.md`;
- `AGENT.md`;
- other relevant project documentation.

Do not modify sibling-service source code, configuration, tests, or documentation.

The only permitted cross-service write is removal or maintenance of synchronization tasks in the synchronization file intended for the current target service as defined by this skill.

### 6.3 Workspace Boundary Exception

Read-only access to an explicitly related sibling service is allowed for cross-session contract analysis even when that sibling service is outside the current service's normal working directory.

This exception exists only for:

- understanding the dependency;
- diagnosing cross-service behavior;
- verifying a contract;
- forming or completing a synchronization task.

It does not authorize code changes outside the owned service.

## 7. Task Ownership Lifecycle

Use the following lifecycle:

1. a source session identifies work required from another service;
2. the source session adds a task to the target service's `*_SYNC.md`;
3. the target service session reads the task;
4. the target service implements it;
5. the target service verifies it completely;
6. the target service removes the completed task from its synchronization file.

The source session does not need to return later only to confirm task completion or remove the task.

The target service owns completion cleanup after successful implementation and verification.

## 8. Atomic Task Rule

Each synchronization entry must describe one independently understandable and verifiable result.

Good:

```md
- [ ] Add `GET /api/subscriptions/current`.
  - Requested by: frontend
  - Input: authenticated user
  - Output: `id`, `status`, `expires_at`
  - Acceptance: returns the current subscription or `null` when none exists
```

Bad:

```md
- [ ] Finish subscriptions, fix auth, improve the database, and update the API.
```

If a task contains multiple independent responsibilities, split it into separate tasks.

## 9. Minimum Task Contract

Each synchronization task must include enough information for the target service to implement it without reconstructing the request from unrelated chat history.

Include, when applicable:

- concise task title or required change;
- requesting service;
- method and path for API work;
- required input;
- required output;
- required fields or schema behavior;
- error behavior;
- nullable behavior;
- compatibility constraints;
- acceptance criteria;
- blocking dependency.

Do not add empty metadata fields only to satisfy a template.

Recommended compact format:

```md
- [ ] Add current subscription endpoint.
  - Requested by: frontend
  - Requirement: `GET /api/subscriptions/current`
  - Input: authenticated user
  - Output: `id`, `status`, `expires_at`
  - Acceptance: returns `200`; subscription may be `null`
  - Blocks: subscription page integration
```

## 10. Optional `Blocks` Field

Use `Blocks` only when it helps the target service understand what cannot be completed until the contract is implemented.

Example:

```md
- Blocks: subscription page integration
```

This is not a priority system.

It is only a compact dependency explanation.

Omit it when the dependency is already obvious.

## 11. Contract-First Cross-Service Work

When one service requires an API or another formal interface from another service, define the expected contract before or together with the task.

For API work, include applicable details such as:

- method;
- path;
- authentication expectations;
- input;
- output;
- errors;
- nullable behavior;
- required fields.

Do not write vague tasks such as:

```text
Need subscription API.
```

Write the contract clearly enough that both sessions can implement against the same expectation.

## 12. Safe Contract Review

A synchronization task represents a required product or integration need, not permission to violate architecture, security, or project constraints.

If the requested contract is unsafe, contradictory, or technically inappropriate:

1. analyze the problem;
2. do not implement the unsafe design blindly;
3. explain the issue to the user when the change is material;
4. propose the safer or more coherent contract;
5. update the synchronization task or create reverse synchronization work when necessary after the decision is established.

Security and architectural correctness take precedence over literal implementation of a flawed contract.

## 13. Duplicate Task Prevention

Before adding a new synchronization task:

1. inspect the existing target synchronization file;
2. check whether the same requirement already exists;
3. reuse or extend the existing task when appropriate;
4. avoid adding semantically duplicate checkboxes.

Do not create multiple tasks for the same contract simply because wording differs.

If an existing task is missing a required detail, update that task instead of creating a duplicate.

## 14. Documentation References Instead of Duplication

Synchronization files must contain only pending cross-session operational contracts.

Do not store:

- project architecture;
- service descriptions;
- generic coding rules;
- completed task history;
- RoadMap content;
- deployment guides;
- long implementation notes;
- general technical documentation.

When background information already exists, reference the relevant documentation instead.

Examples:

```md
- Architecture context: see `README.md`.
- Internal service constraints: see `AGENT.md`.
```

Do not copy large sections from those files into `*_SYNC.md`.

## 15. Task Priority

Incoming synchronization tasks do not automatically override the user's current explicit task.

Use this priority order:

1. the user's current explicit task;
2. synchronization dependencies required to complete that task;
3. other pending synchronization tasks explicitly requested by the user;
4. unrelated pending synchronization tasks remain pending.

Do not automatically process every task in the synchronization file simply because it exists.

When unrelated pending tasks are present, report them when relevant but do not start them without user direction.

## 16. Dependency Integration with the Current Task

If the user's current task directly corresponds to or depends on an existing synchronization task, that synchronization task may be implemented as part of the current task.

No separate permission is required merely because the same requirement is already written in the sync file.

After full implementation and verification, remove the completed sync task.

## 17. Completion and Removal

A synchronization task is complete only when its required behavior has been implemented and all applicable verification has passed.

Examples of applicable verification:

- unit tests;
- integration tests;
- API contract tests;
- runtime checks;
- schema verification;
- negative-case checks;
- service startup checks;
- log inspection.

Do not remove a task immediately after writing the code.

Remove it only when the contract is fully implemented and verified.

If required verification cannot be completed, keep the task pending until sufficient validation is possible.

Do not convert completed tasks to `[x]`.

Delete them entirely after successful completion.

## 18. Reverse Synchronization

If implementing an incoming task creates new required work for another service, create or update the appropriate reverse synchronization task.

Example:

```text
Frontend requests backend endpoint
        ↓
Backend implements endpoint
        ↓
Backend contract requires frontend field handling change
        ↓
Backend adds task to FRONTEND_SYNC.md
```

Do not assume the other service will discover the new requirement automatically.

Reverse tasks must follow the same atomic-contract rules.

## 19. Contract Changes During Implementation

If implementation requires changing an established inter-service contract:

- identify the affected dependent service;
- update the appropriate synchronization task;
- create reverse work when necessary;
- do not silently change method, path, schema, nullability, authentication behavior, or error semantics when another service depends on the previous contract.

Cross-service contract changes must remain visible to all affected sessions.

## 20. No Hidden Assumptions

Do not record unimplemented cross-service behavior as if it already exists.

A pending synchronization task means the dependency is still unresolved.

Do not write project memory such as:

```md
Backend provides `GET /api/subscriptions/current`.
```

until that endpoint is actually implemented and verified.

If the current service uses a temporary mock or fallback while waiting for another service, document it as temporary in the relevant current-service context and keep the synchronization task pending.

## 21. Secret Protection

Never place real secrets in synchronization files.

Do not include:

- passwords;
- API keys;
- access tokens;
- refresh tokens;
- bot tokens;
- private keys;
- signing secrets;
- database credentials;
- production credentials.

Variable names may be referenced when necessary.

Good:

```md
- Requires `TELEGRAM_BOT_TOKEN`.
```

Forbidden:

```md
- TELEGRAM_BOT_TOKEN=actual-secret-value
```

## 22. Cross-Session Handoff Requirement

If the current task discovers a requirement owned by another service, the task handoff is incomplete until that dependency is recorded in the appropriate synchronization file.

Example:

```text
Frontend task
    ↓
requires missing backend endpoint
    ↓
BACKEND_SYNC.md task created
```

Do not finish the task with the dependency mentioned only in chat.

The synchronization file is the operational handoff mechanism.

## 23. Incoming Task Completion Requirement

If the current task implements an incoming synchronization contract:

1. complete the implementation;
2. run all applicable verification;
3. update related documentation if required by project documentation rules;
4. create reverse synchronization tasks if new cross-service work was introduced;
5. remove the completed incoming task from the target synchronization file.

Do not leave fully completed tasks behind.

## 24. User-Facing Reporting

When the current task creates cross-session work, report it in the final handoff.

Example:

```text
Cross-session dependency:
- Added to BACKEND_SYNC.md: GET /api/subscriptions/current
- Full frontend integration depends on backend implementation
```

When an incoming task is completed:

```text
Cross-session contract:
- BACKEND_SYNC.md task implemented and verified
- Completed task removed from the synchronization file
```

When pending tasks exist but were not requested or required:

```text
Pending cross-session tasks:
- 2 BACKEND_SYNC.md tasks remain pending
- They were not required for the current task and were not started
```

Keep reporting concise.

## 25. Definition of Done

A cross-session coordination task is complete only when all applicable conditions are satisfied:

- the correct target synchronization file was used;
- the file name follows `<TARGET_SERVICE>_SYNC.md`;
- the synchronization file remains local and excluded from Git tracking;
- the file uses English only;
- the standard header is present;
- the task is atomic;
- the task contains sufficient contract information;
- duplicate tasks were avoided;
- related documentation was referenced instead of duplicated;
- no real secrets are present;
- the source session did not modify another service's source code;
- sibling-service access remained read-only except for allowed synchronization-file maintenance;
- the user's explicit current task retained priority;
- unrelated pending sync tasks were not started automatically;
- unsafe or invalid contracts were not implemented blindly;
- incoming tasks were removed only after full implementation and verification;
- completed tasks were deleted rather than archived as `[x]`;
- reverse synchronization was created when implementation introduced new cross-service work;
- pending cross-service assumptions were not recorded as implemented facts;
- newly discovered dependencies were written to the appropriate synchronization file before handoff;
- cross-session additions, completions, or remaining blockers were reported to the user.

If any applicable requirement cannot be completed or verified, leave the affected synchronization task pending and report the limitation explicitly.
