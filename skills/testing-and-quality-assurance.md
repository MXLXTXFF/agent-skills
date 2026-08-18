---
name: Testing and Quality Assurance
description: >
  Governs automated and manual testing, quality verification, regression
  protection, Docker-first test execution, isolated test data, negative and
  boundary testing, flaky-test handling, runtime smoke checks, and final
  quality-gate reporting for development tasks.
---

# Testing and Quality Assurance

## 1. Purpose

Use this skill whenever code, configuration, API behavior, database behavior, integrations, background jobs, dependencies, or user-visible functionality are created, changed, fixed, refactored, or prepared for release.

The goal is to verify real behavior, prevent regressions, keep tests isolated and reproducible, and provide an honest quality status before a task is considered complete.

This skill governs:

- unit tests;
- integration tests;
- API and contract tests;
- end-to-end tests;
- regression tests;
- negative tests;
- boundary tests;
- fixtures and test factories;
- mocks and stubs;
- deterministic test data;
- flaky-test handling;
- runtime smoke verification;
- log inspection;
- manual verification when automation is impractical;
- quality-gate classification;
- concise testing handoff.

This skill does not replace:

- Security Audit;
- Database Development and Migration Management;
- API Development;
- Performance and Load Testing;
- CI/CD management.

Use those skills where applicable.

## 2. Activation

Apply this skill automatically when a task includes:

- new functionality;
- bugfixes;
- refactoring;
- API changes;
- database changes;
- background jobs;
- external integrations;
- dependency upgrades;
- runtime configuration changes;
- vulnerability remediation;
- release preparation.

Do not require the user to explicitly ask for tests.

## 3. Risk-Based Test Scope

Choose test depth according to the change and its risk.

Before testing, determine:

- what changed;
- which components are affected;
- which contracts may break;
- which existing behavior depends on the changed code;
- which failure modes are realistic;
- which user or system flows are critical.

Do not require every change to use every test type.

## 4. Test Pyramid

Use the test pyramid as guidance, not as a rigid quota.

Prefer:

```text
many fast unit tests
→ fewer integration tests
→ fewer high-value E2E tests
```

Choose the layer that best verifies the actual risk.

## 5. Docker-First Testing

All testing, development verification, auxiliary services, and temporary tooling should run through Docker by default.

Priority:

```text
existing project Docker environment
→ temporary Docker container
→ already available host tool
→ host modification or installation only after explicit approval
```

If a suitable Docker-based path exists, prefer it.

### 5.1 Host-Side Work

Do not install or permanently modify host software without explicit user approval.

Examples requiring approval:

```text
apt install ...
pip install system-wide ...
npm install -g ...
system package changes
persistent host configuration changes
```

An operation outside Docker may be used without additional approval only when the agent is fully confident that it:

- is temporary;
- leaves no persistent dependency;
- does not modify host configuration;
- does not install software;
- does not create lasting system state.

When uncertain, ask the user.

## 6. Runtime Parity

Important verification should reflect the real runtime environment.

Avoid:

```text
local tests pass
→ Docker application fails
```

For meaningful application changes, final verification should include:

```text
automated tests
→ Docker startup
→ runtime smoke check
→ log inspection
```

## 7. Unit Tests

Use unit tests for isolated logic such as:

- calculations;
- parsers;
- validators;
- transformations;
- state transitions;
- permission functions;
- domain rules.

Do not start a full database or application stack when the behavior can be tested meaningfully in isolation.

## 8. Integration Tests

Use integration tests for real component interactions such as:

```text
application ↔ database
application ↔ Redis
service ↔ service
API ↔ persistence
worker ↔ queue
```

Prefer real project technologies when their behavior is the subject of the test.

## 9. End-to-End Tests

Use E2E tests for critical user or system workflows.

Examples:

```text
register
→ login
→ create subscription
→ retrieve subscription
```

or:

```text
payment webhook
→ backend
→ database
→ subscription activation
```

Do not create expensive E2E coverage for every small internal function.

## 10. Test Database Isolation

Tests must never use active development, staging, or production databases for temporary data.

Follow `Database Development and Migration Management`.

Use:

- dedicated test database;
- temporary database clone;
- ephemeral database container.

Test data must not pollute the main database even temporarily.

## 11. Positive Tests

Every meaningful feature should have a valid happy-path verification.

A happy path alone is not sufficient for significant behavior.

## 12. Negative Tests

Test applicable failure scenarios such as:

- missing input;
- invalid input;
- wrong type;
- unauthorized access;
- forbidden access;
- not found;
- conflict;
- dependency unavailable;
- timeout;
- duplicate request;
- invalid state transition.

## 13. Boundary Tests

Test meaningful boundaries such as:

```text
0
1
minimum
maximum
maximum + 1
empty
very long
expiration boundary
```

Pay particular attention to:

- pagination;
- limits;
- money;
- durations;
- file size;
- rate limits;
- numeric fields.

## 14. Missing, Null, Empty, and False Values

When semantics differ, test separately:

```text
missing
null
""
[]
{}
0
false
```

Do not assume they are interchangeable.

## 15. Bugfix Regression-First Workflow

For bugfixes, reproduce the bug with a test before fixing it whenever technically practical.

Preferred workflow:

```text
reproduce bug
→ create failing regression test
→ confirm it fails
→ implement fix
→ confirm the test passes
→ run related regression tests
```

This does not make full TDD mandatory for all development.

If a bug cannot reasonably be automated, explain why and perform reliable manual verification.

## 16. Security Regression Tests

For fixed security defects, reproduce the previously unsafe behavior in a controlled test when practical.

Deep security analysis belongs to `Security Audit`.

## 17. Characterization Tests

Before refactoring poorly tested legacy code, create characterization tests when necessary to preserve current intended behavior.

## 18. Test Behavior, Not Implementation Details

Prefer tests based on observable behavior.

Avoid coupling tests to unnecessary private implementation details.

## 19. Do Not Test the Framework Instead of the Project

Focus tests on:

- domain logic;
- validation;
- permissions;
- contracts;
- integrations;
- edge cases.

Do not create excessive tests that merely prove the framework performs standard documented behavior.

## 20. Mock Policy

Use mocks where they provide clear value.

Good candidates:

- external APIs;
- payment providers;
- Telegram API;
- email providers;
- slow or nondeterministic third-party systems.

Do not mock internal project infrastructure so aggressively that integration defects become invisible.

## 21. External Integration Testing

Use two layers where practical.

### 21.1 Deterministic Local Layer

Use mock or stub scenarios such as:

```text
provider success
provider error
timeout
invalid response
```

### 21.2 Real Sandbox Layer

Use a provider sandbox/test environment when available and appropriate.

Do not automatically perform production or paid external operations.

## 22. Fixtures

Fixtures should be:

- minimal;
- deterministic;
- understandable;
- reusable only where reuse improves clarity.

Do not create huge hidden fixture graphs for small tests.

## 23. Test Factories

For larger suites, use small factories where they reduce duplication.

Do not introduce a complex factory framework when direct fixtures are clearer.

## 24. Deterministic Test Data

Prefer deterministic input.

If randomness is required:

- allow a reproducible seed;
- capture enough information to reproduce failures.

## 25. Time-Dependent Tests

Avoid real waiting when time can be controlled.

Prefer:

```text
fixed clock
→ advance time
→ verify expiration
```

instead of long sleeps when framework support allows it.

## 26. Avoid Artificial Sleeps

Do not use `sleep()` as generic synchronization.

For asynchronous or background behavior, wait for the actual condition or event with a defined timeout.

## 27. Async Tests

For asynchronous code, test actual async behavior when relevant.

Consider:

- cancellation;
- timeout;
- parallel execution;
- race conditions;
- duplicate job execution.

## 28. Concurrency Tests

Test race conditions for operations where concurrent execution can break correctness.

Examples:

- payments;
- subscription activation;
- unique resource creation;
- balance or quota changes;
- one-time operations.

## 29. Idempotency Tests

When an API, worker, or webhook is intended to be idempotent:

```text
same request twice
→ same logical result
→ no duplicate side effect
```

## 30. Contract Tests

When services depend on a shared contract, verify applicable:

- schema;
- fields;
- nullability;
- errors;
- enums;
- event payloads;
- backward compatibility.

Use `API Development` for contract design.

## 31. Database Tests

For database changes, follow `Database Development and Migration Management`.

Verify applicable:

```text
fresh database
existing database upgrade
constraints
data preservation
migration behavior
```

## 32. Coverage Philosophy

Do not enforce a universal coverage percentage such as 80%, 90%, or 100%.

Coverage is a diagnostic metric, not the quality goal itself.

Use coverage to identify important untested logic.

## 33. Critical Logic Coverage

Prioritize meaningful coverage for:

- authentication;
- authorization;
- payments;
- subscriptions;
- state machines;
- data transformations;
- migrations;
- business-critical workflows.

## 34. Existing Test Failures

Record the baseline before significant work.

After the task, distinguish:

- existing failures;
- new failures;
- resolved failures.

Do not attribute pre-existing failures to the current change.

## 35. New Failures

A new failure introduced by the current change means the task is not fully complete unless the user explicitly accepts the broken state.

Do not delete or weaken a test merely to make the suite green.

## 36. Changing Test Expectations

When a test fails after implementation, determine first:

```text
implementation bug?
or
intentional contract change?
```

Change expected behavior only when the intended contract genuinely changed.

## 37. Flaky Tests

Do not repeatedly rerun a flaky test until it passes and then report success.

Investigate, fix where practical, or report the unresolved flaky status.

## 38. Test Retries

Retries may be used diagnostically.

They must not hide instability.

If a test passes only after retry, report that fact.

## 39. Test Timeouts

Potentially hanging tests should have reasonable scenario-specific timeouts.

Especially:

- network operations;
- WebSocket;
- queues;
- workers;
- external integrations.

## 40. Log Inspection

After integration and runtime verification, inspect logs.

A green test suite is not sufficient when logs show:

- unhandled exceptions;
- database errors;
- connection leaks;
- retry storms;
- unexpected warnings.

## 41. Warnings

Analyze new warnings.

Pay attention to:

- deprecations;
- resource leaks;
- connection warnings;
- unsafe configuration;
- runtime compatibility warnings.

## 42. Runtime Smoke Verification

For meaningful backend or service changes, perform a Docker-based smoke check.

Typical flow:

```text
start project
→ health check
→ critical endpoint or action
→ inspect logs
```

## 43. Smoke Tests

Use a small set of fast critical checks when useful.

Examples:

```text
application starts
health endpoint works
database connection works
basic authentication works
```

Smoke tests do not replace deeper verification.

## 44. Test Execution Order

Prefer:

```text
fast targeted tests
→ affected integration tests
→ broader regression
→ E2E where applicable
→ Docker runtime smoke
```

## 45. Testing After Implementation Phases

For phased refactoring or large work:

```text
phase
→ targeted tests
→ relevant regression
→ logical commit
```

Do not defer all verification until the end.

## 46. Full Relevant Suite

Run the full relevant suite when applicable:

- after large changes;
- before release;
- after several related implementation phases;
- after changes to shared/core logic;
- when regression risk is high.

Do not run expensive unrelated tests for documentation-only changes.

## 47. Manual Verification

When meaningful automation is impractical, use a clear manual verification checklist.

Report explicitly:

```text
Automated verification: unavailable
Manual verification: completed
```

or:

```text
Verification: unverified
```

## 48. Test Code Quality

Test code is part of project quality.

Keep it:

- readable;
- maintainable;
- deterministic;
- reasonably deduplicated;
- free from hidden test-order dependencies.

Do not overengineer the test framework.

## 49. Regression Test Preservation

Do not remove a useful regression test merely because implementation changed.

If behavior remains required, preserve verification of that behavior.

## 50. Obsolete Tests

A test may be removed when:

- the feature was removed;
- the contract intentionally changed;
- the test is genuinely redundant;
- the tested behavior no longer exists.

Before removal, verify that important risk coverage is not lost.

## 51. Test Naming

Use names that explain behavior.

Good:

```text
test_user_cannot_access_another_users_subscription
```

Avoid vague names such as:

```text
test_subscription_1
```

## 52. Arrange / Act / Assert

Prefer a readable conceptual structure:

```text
Arrange
Act
Assert
```

Do not require literal comments when the test is already clear.

## 53. Diagnostic Assertions

Failures should make it clear:

- what was expected;
- what actually happened;
- which scenario failed.

## 54. Resource Cleanup

Tests must clean up applicable:

- database connections;
- files;
- sockets;
- processes;
- containers;
- temporary directories.

Do not leave zombie processes or unmanaged temporary resources.

## 55. Parallel Test Safety

When tests run in parallel:

- avoid shared mutable global state;
- avoid port collisions;
- use isolated filenames and directories;
- isolate database records;
- avoid ordering dependencies.

## 56. Test Independence

Each test should create or define the state it requires.

Do not rely on another test having run first.

## 57. Test Secrets

Do not use real production secrets in tests.

Use:

- fake credentials;
- test credentials;
- provider sandbox credentials through environment configuration.

Do not commit real tokens in fixtures, snapshots, or test source.

## 58. Snapshot Tests

Use snapshot tests where they provide real value.

Do not automatically update snapshots simply because tests fail.

Review the changed output first.

## 59. Golden Files

Treat changes to golden/reference output as behavior changes.

Do not replace expected files blindly to obtain a passing result.

## 60. Performance Boundary

This skill may verify ordinary timeout behavior, resource limits, and basic responsiveness.

Deep:

- RPS;
- p95/p99 latency;
- stress testing;
- soak testing;
- capacity testing

belongs to a dedicated `Performance and Load Testing` skill.

## 61. Quality Gate Status

Before handoff, classify quality status.

### Passed

All required verification passed.

### Passed with Existing Issues

Current work passed, but unrelated pre-existing failures or warnings remain.

### Partially Verified

Some required verification could not be completed.

### Failed

New failures or unresolved regressions exist.

Do not report a task as fully complete when verification is incomplete.

## 62. Testing Handoff

Do not create a permanent `TEST_REPORT.md` for ordinary development tasks.

Provide a concise testing summary in chat.

Example:

```text
Testing:
- Unit: 42 passed
- Integration: 18 passed
- Regression: passed
- Docker runtime: passed
- New failures: 0
- Existing failures: 2 unrelated
- Unverified: none
- Quality status: Passed with Existing Issues
```

Create a detailed test report only when the user explicitly requests one.

## 63. Definition of Done

Testing and quality verification are complete only when all applicable conditions are satisfied:

- test scope was chosen according to change risk;
- Docker was used as the preferred execution environment;
- no unapproved persistent host software or configuration changes were made;
- unit tests were added or updated where appropriate;
- integration tests were used for real component interactions where appropriate;
- E2E tests were used only for high-value workflows where appropriate;
- test data did not use active development, staging, or production databases;
- positive behavior was verified;
- negative behavior was verified where applicable;
- boundary cases were verified where applicable;
- missing/null/empty semantics were checked where relevant;
- bugfixes received regression-first coverage where practical;
- security fixes received regression tests where practical;
- characterization tests protected legacy behavior where needed;
- tests focus on behavior rather than unnecessary implementation details;
- mocks were used only where justified;
- external provider behavior was tested deterministically;
- sandbox verification was used where useful and safe;
- fixtures are deterministic and understandable;
- random failures are reproducible;
- time-dependent tests avoid unnecessary real waiting;
- artificial sleeps were avoided where proper synchronization is possible;
- async and concurrency risks were tested where relevant;
- idempotency was tested where required;
- contracts were verified where required;
- database behavior followed the database-management skill;
- coverage was used diagnostically rather than as a universal percentage target;
- critical business logic received appropriate coverage;
- baseline test failures were distinguished from new failures;
- new failures were not ignored;
- expected values were not changed merely to make tests pass;
- flaky tests were not hidden by retries;
- test timeouts are reasonable;
- logs were inspected after relevant runtime tests;
- new warnings were reviewed;
- Docker runtime smoke verification passed for meaningful service changes;
- broader regression was run when risk required it;
- manual verification was clearly identified when automation was unavailable;
- test code remains maintainable;
- useful regression tests were preserved;
- obsolete tests were removed only with justification;
- tests are independently runnable;
- parallel execution does not rely on shared mutable state;
- test resources are cleaned up;
- no real production secrets are embedded in tests;
- snapshot and golden-file changes were reviewed intentionally;
- deep performance testing was not falsely claimed;
- final quality status was classified honestly;
- the user received a concise testing handoff;
- no permanent test-report file was created unless explicitly requested.

If an applicable verification cannot be completed, mark it explicitly as unverified rather than claiming the task is fully validated.
