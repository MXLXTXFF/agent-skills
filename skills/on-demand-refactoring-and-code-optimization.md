---
name: On-Demand Refactoring and Code Optimization
description: >
  Governs explicitly requested audits, refactoring, and performance optimization
  of existing codebases, including baseline capture, analysis-first planning,
  scope approval, behavior preservation, characterization testing, phased
  implementation, benchmarking, rollback discipline, documentation updates,
  logical commits, and final before/after reporting.
---

# On-Demand Refactoring and Code Optimization

## 1. Purpose

Use this skill only when the user explicitly requests a code audit, refactoring effort, code optimization, or a combined review of an existing codebase.

The goal is to improve maintainability, safety, clarity, and efficiency without silently changing established behavior, architecture, public interfaces, or operational contracts.

This skill must never activate automatically merely because the agent notices code that could be cleaner, faster, or more modern during ordinary development.

When potential refactoring or optimization opportunities are noticed outside an explicitly requested audit, mention them as recommendations instead of applying this skill automatically.

## 2. Activation Modes

The skill supports three modes.

### 2.1 Audit Only

Use when the user requests analysis, review, or audit without implementation.

Workflow:

```text
baseline
→ deep analysis
→ findings
→ recommendations
→ no code changes
```

Do not modify project files in Audit Only mode unless the user later explicitly approves implementation.

### 2.2 Refactoring

Use when the user explicitly requests behavior-preserving restructuring or code-quality improvement.

Workflow:

```text
baseline
→ analysis
→ approved plan
→ characterization tests when needed
→ phased implementation
→ verification
→ final report
```

### 2.3 Optimization

Use when the user explicitly requests measurable performance, resource, database, build, or runtime optimization.

Workflow:

```text
baseline metrics
→ bottleneck identification
→ approved plan
→ optimization
→ benchmark
→ compare before/after
→ final verification
```

If the user request combines modes, apply the relevant rules from each mode.

## 3. Baseline Capture

Before making any code changes:

- inspect the current project state;
- run existing tests in Docker;
- start or verify affected services in the Docker environment;
- inspect relevant logs;
- record current failures, warnings, and known defects;
- record relevant performance metrics when optimization is requested;
- identify existing uncommitted changes and preserve them according to repository rules.

The baseline does not need to be perfect.

If existing tests already fail, record the exact baseline instead of treating the project as automatically blocked.

Example:

```text
Baseline:
- 97 tests passed
- 3 tests failed
- application starts successfully
- warning X already exists
```

After refactoring or optimization, the result must not silently worsen this baseline.

## 4. Analysis Before Changes

No implementation may begin before analysis is completed.

The first phase must inspect the relevant codebase and produce a detailed change plan without modifying project files.

The analysis should identify:

- maintainability problems;
- duplicated logic;
- oversized modules;
- coupling;
- unclear responsibilities;
- dead or obsolete code candidates;
- unsafe or fragile behavior;
- database inefficiencies;
- runtime bottlenecks;
- resource inefficiencies;
- Docker build or runtime inefficiencies;
- testability gaps;
- missing characterization coverage;
- architecture inconsistencies within the requested scope.

## 5. Plan Structure

Organize the preliminary plan into the following categories when applicable:

- Refactoring;
- Security;
- Efficiency.

Do not create empty categories.

For every meaningful finding, include:

- Problem;
- Impact;
- Risk;
- Proposed change;
- Why the change is preferable;
- Verification method.

Recommended format:

```md
### Refactoring

#### Authentication service decomposition

Priority: High

Problem:
`auth_service.py` combines token generation, session persistence, and authorization checks.

Impact:
High coupling and difficult isolated testing.

Proposed change:
Split token, session, and authorization responsibilities into dedicated modules.

Why:
Reduces coupling and makes each responsibility independently testable.

Risk:
Medium — authentication flow is affected.

Verification:
Existing auth tests plus login, refresh, and logout integration checks.
```

## 6. Finding Priority

Use the following priority levels when useful:

- Critical;
- High;
- Medium;
- Low.

Prioritize based on actual impact, not presentation value.

Examples:

- `Critical` — exploitable authorization bypass or serious data-loss risk;
- `High` — major runtime bottleneck, severe coupling, or important correctness risk;
- `Medium` — duplicated logic, maintainability issue, or contained inefficiency;
- `Low` — naming cleanup, minor structure improvement, or cosmetic simplification.

Do not inflate priority without technical justification.

## 7. User Approval and Scope Freeze

After presenting the plan, wait for explicit user approval before modifying code.

Once approved, the implementation scope is frozen to the accepted plan.

Do not silently add major refactors, dependency upgrades, architecture changes, or unrelated optimizations during implementation.

If new significant work is discovered:

1. record it;
2. explain why it matters;
3. propose it separately;
4. obtain explicit approval before adding it to the active scope.

Minor local adjustments required to safely complete an already approved change do not require separate approval if they do not materially change scope, architecture, public behavior, or compatibility.

## 8. Behavior and Contract Preservation

Refactoring must preserve existing key behavior unless a change is explicitly approved in the plan.

Preserve applicable contracts such as:

- public API methods and paths;
- request schemas;
- response schemas;
- error semantics;
- authentication behavior;
- authorization behavior;
- database semantics;
- environment variables;
- public configuration;
- external integrations;
- inter-service contracts;
- user-visible behavior;
- operational startup behavior.

If changing any of these is necessary, the plan must explain:

- what changes;
- why the current behavior is insufficient;
- how the change improves correctness, maintainability, safety, or performance;
- what compatibility impact exists;
- how the change will be verified.

## 9. Characterization Tests

Before risky refactoring of poorly tested legacy or complex behavior, add characterization tests when needed.

Characterization tests should capture the existing externally observable behavior before restructuring begins.

Use them to distinguish:

- intended behavior preservation;
- accidental regressions introduced by refactoring.

Do not rewrite unclear legacy behavior before first understanding and, when practical, capturing what the code currently does.

## 10. Refactoring Rules

Refactoring may include:

- module decomposition;
- responsibility separation;
- duplication removal;
- control-flow simplification;
- naming cleanup;
- project-structure normalization;
- testability improvement;
- removal of truly obsolete code;
- replacement of fragile internal structure.

Do not change behavior merely to make the code shorter.

Maintainability, correctness, readability, cohesion, and testability matter more than reducing line count.

## 11. Dead Code Removal

Remove code only when there is sufficient evidence that it is not used.

Do not classify code as dead solely because a basic text search finds no direct caller.

Check applicable runtime mechanisms such as:

- schedulers;
- framework callbacks;
- dynamic imports;
- CLI entry points;
- reflection;
- dependency injection;
- plugin registration;
- event handlers;
- background jobs;
- migration hooks;
- external invocation.

If usage remains uncertain, do not delete the code as part of an approved cleanup without clarifying the risk.

## 12. Security Scope

Security review in this skill is limited to the requested refactoring or optimization scope.

Check for applicable issues such as:

- security regressions;
- unsafe input handling;
- authorization mistakes;
- injection risks;
- secret exposure;
- insecure configuration handling;
- dependency issues directly related to the approved work.

Do not expand this skill into a full system-wide security audit, threat model, or complete OWASP review unless the user explicitly requests such work through the dedicated security process.

## 13. Performance Optimization Principles

Do not optimize based only on intuition.

Use:

```text
measure
→ identify bottleneck
→ optimize
→ measure again
```

A performance optimization must have an identified or clearly reproducible bottleneck.

Potential areas include:

- response latency;
- database queries;
- query count;
- execution plans;
- CPU usage;
- memory usage;
- Docker image size;
- build time;
- startup time;
- I/O;
- serialization;
- repeated computation.

Do not add complexity when measurement does not justify it.

## 14. Real Benchmark Requirement

Performance claims must be based on real measurements.

Do not claim:

```text
Performance improved by ~40%.
```

unless a benchmark or equivalent measurement was actually performed.

Report metrics as:

```text
Before: 420 ms
After: 95 ms
```

or:

```text
Queries: 101 → 3
```

If measurement is unavailable, report:

```text
Performance impact: unverified
```

Never invent benchmark results.

## 15. Code Size as a Metric

Code-size reduction may be reported when useful, but it is not a standalone success metric.

For example:

```text
500 LOC → 320 LOC
```

does not automatically mean the code improved.

Evaluate code-quality success through:

- maintainability;
- correctness;
- clarity;
- cohesion;
- complexity;
- testability;
- performance;
- resource usage.

## 16. Database Optimization

For database-related optimization:

1. identify the actual query pattern;
2. measure or inspect query behavior;
3. detect N+1 or inefficient access patterns;
4. inspect execution plans for performance-sensitive paths when practical;
5. justify indexes or schema changes;
6. apply the approved optimization;
7. measure again.

Do not add indexes mechanically because a field appears in `WHERE`, `JOIN`, or `ORDER BY`.

Do not change schema solely to satisfy generic optimization rules.

For pre-release MVP databases, broader restructuring may be acceptable when approved and technically justified.

## 17. Docker and Build Optimization

Docker optimization may target:

- image size;
- build duration;
- cache efficiency;
- startup duration;
- runtime memory;
- runtime CPU usage.

Do not make Dockerfiles substantially more complex for negligible benefit.

Optimization must not unnecessarily reduce:

- reproducibility;
- readability;
- predictable caching;
- debugging ability;
- security.

## 18. Dependency Upgrade Boundary

Do not perform unrelated major dependency or stack upgrades as part of refactoring or optimization.

Major upgrades require separate analysis and approval.

Allowed dependency changes are limited to:

- explicitly approved upgrades;
- security fixes required by the approved scope;
- minimal dependency changes necessary to implement an approved refactor or optimization.

Do not silently modernize the entire stack.

## 19. Phased Implementation

Implement approved work in logical phases.

Typical order:

```text
Refactoring
→ verification
→ commit

Security
→ verification
→ commit

Efficiency
→ verification + benchmark
→ commit

Final full verification
```

Only include phases that are actually needed.

Run relevant tests and checks after each phase before continuing.

This allows regressions to be isolated to the phase that introduced them.

## 20. Logical Commits

Create logical local commits for completed and verified work.

Use Conventional Commit types appropriate to the change:

- `refactor:` for structural or maintainability changes;
- `fix:` for security or correctness fixes;
- `perf:` for performance improvements;
- other standard types when clearly more appropriate.

Do not create an empty category commit.

Do not combine many unrelated changes into one oversized commit when they can be separated coherently.

Examples:

```text
refactor(auth): split token responsibilities
refactor(subscription): remove duplicated state transitions
fix(auth): enforce ownership validation
perf(database): reduce subscription query count
```

Push remains subject to the repository operation rules and requires explicit user approval.

## 21. Rollback Discipline

Use Git history as the primary rollback mechanism for refactoring and optimization phases.

Maintain clear phase boundaries so unsuccessful work can be reverted without disturbing unrelated changes.

If a phase introduces persistent failures or becomes significantly more complex than expected:

1. stop expanding the fix blindly;
2. identify whether the approved approach remains valid;
3. when appropriate, roll back the failed phase;
4. update the plan based on the discovered constraint;
5. request approval if the new approach materially changes the accepted scope.

Do not spend unlimited time layering fixes over an invalid refactoring direction when a clean rollback is safer.

Pay extra attention to rollback implications for:

- migrations;
- configuration changes;
- dependency changes;
- infrastructure changes.

## 22. Documentation and Cross-Session Synchronization

After implementation, check whether changes require updates to:

- `README.md`;
- `AGENT.md`;
- relevant `*_SYNC.md` files.

Examples:

- internal architecture changed → update `AGENT.md`;
- startup or public behavior changed → update `README.md`;
- an inter-service contract changed → update the appropriate synchronization file;
- new work is required from another service → create a reverse synchronization task.

Do not leave project documentation or cross-session contracts stale after an approved refactor.

## 23. Final Verification

After all approved phases are complete:

- run the relevant full test suite inside Docker;
- verify affected services start correctly;
- inspect logs for new errors or warnings;
- repeat relevant benchmarks;
- verify public and inter-service contracts;
- compare the final state against the recorded baseline;
- confirm that no new regression was introduced;
- verify documentation and synchronization updates.

Existing baseline failures may remain only if they were documented before the work and were not worsened.

## 24. Final Report

Provide a concise final report organized by the accepted plan.

Recommended structure:

```text
Refactoring
- 4 planned
- 4 completed

Security
- 2 findings
- 2 fixed

Efficiency
- 3 planned
- 2 verified improvements
- 1 rejected because benchmarks showed no benefit

Verification
- 128 tests passed
- Docker startup passed
- no new log errors

Metrics
- DB queries: 27 → 6
- endpoint latency: 310 ms → 145 ms
- image size: unchanged

Unverified
- production-load performance
```

Report:

- completed changes;
- rejected or deferred changes;
- actual metrics;
- remaining risks;
- unverified items;
- documentation updates;
- synchronization updates;
- commits created.

Do not hide failed experiments or optimizations that showed no measurable benefit.

## 25. Definition of Done

An audit, refactoring, or optimization task is complete only when all applicable conditions are satisfied:

- the skill was activated by explicit user request;
- the requested mode was identified;
- the initial baseline was recorded;
- existing failures and warnings were documented;
- analysis was completed before modifications;
- the preliminary plan was presented;
- meaningful findings include problem, impact, risk, proposed change, rationale, and verification;
- priorities reflect actual impact;
- implementation began only after explicit approval;
- approved scope remained frozen unless new work was separately approved;
- public and operational contracts were preserved unless approved changes explicitly altered them;
- characterization tests were added when necessary;
- dead code was removed only with sufficient evidence;
- no unrelated major dependency upgrade was introduced;
- optimization work was based on identified bottlenecks;
- performance claims are supported by real benchmarks;
- database optimization was justified by real query behavior;
- Docker optimization produced meaningful benefit without unnecessary complexity;
- each implementation phase was verified before the next phase;
- logical local commits were created for completed phases;
- Git rollback boundaries remained clear;
- failed or invalid approaches were rolled back when safer than continued patching;
- `README.md` and `AGENT.md` were checked and updated when required;
- relevant synchronization files were checked and updated when required;
- final Docker tests were run;
- affected logs were inspected;
- final benchmarks were performed where applicable;
- the final state was compared against the baseline;
- the final report includes completed, deferred, rejected, measured, and unverified items;
- push was not performed without explicit user approval.

If any applicable verification cannot be completed, mark it explicitly as unverified rather than treating the refactoring or optimization as fully validated.
