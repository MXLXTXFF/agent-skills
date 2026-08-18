---
name: API Development
description: >
  Governs the design, implementation, evolution, verification, and documentation
  of REST, GraphQL, WebSocket, Server-Sent Events, webhook, and other API
  contracts, including protocol selection, compatibility, versioning, errors,
  pagination, idempotency, retries, realtime semantics, contract testing,
  documentation synchronization, and cross-service coordination.
---

# API Development

## 1. Purpose

Use this skill whenever a task creates, changes, extends, deprecates, documents, or verifies an API or inter-service contract.

This skill governs:

- protocol selection;
- REST APIs;
- GraphQL APIs;
- WebSocket APIs;
- Server-Sent Events;
- webhooks;
- message and event contracts;
- request and response schemas;
- error contracts;
- pagination;
- filtering;
- sorting;
- search;
- idempotency;
- retry semantics;
- caching;
- compatibility;
- API versioning;
- deprecation;
- contract testing;
- API documentation;
- cross-service synchronization.

This skill does not replace:

- deep security auditing;
- database design rules;
- project-wide architecture rules;
- public changelog rules.

Use the dedicated skills for those areas when applicable.

## 2. Activation

Apply this skill automatically when the task includes work such as:

- adding an endpoint;
- changing an API response;
- changing an API request;
- creating a webhook;
- creating or changing a WebSocket;
- creating or changing an SSE stream;
- creating or changing GraphQL schema, query, mutation, or subscription behavior;
- adding pagination;
- adding filtering or sorting;
- changing API versioning;
- defining a new inter-service contract;
- changing an existing public or internal API contract.

Do not require a separate explicit request merely to activate this skill.

## 3. Protocol Selection

Choose the protocol according to the interaction model.

### 3.1 REST

Prefer REST or normal HTTP request-response APIs for:

- CRUD;
- commands;
- configuration;
- ordinary service operations;
- bounded synchronous request-response flows.

### 3.2 GraphQL

Use GraphQL when the product materially benefits from:

- client-selected fields;
- typed graph-shaped data;
- related-resource traversal;
- a schema-driven query model.

Do not choose GraphQL merely because it is modern or flexible.

### 3.3 Server-Sent Events

Use SSE when the communication is primarily:

```text
server → client
```

and the client does not require a persistent bidirectional messaging channel.

### 3.4 WebSocket

Use WebSocket when the communication genuinely requires:

```text
client ↔ server
```

over a persistent bidirectional connection.

### 3.5 Webhooks

Use webhooks when one service must asynchronously notify another service without keeping a connection open.

## 4. Material Protocol Decisions

If the user does not specify a protocol and the choice materially affects architecture, explain the available options, recommend one, and obtain approval before implementing the architecture-level decision.

Example:

```text
Requirement:
Frontend must receive subscription status updates.

Option A:
SSE — simple server-to-client event stream.

Option B:
WebSocket — persistent bidirectional channel.

Recommendation:
SSE, because the client only needs server-originated updates.
```

Do not interrupt for approval when the protocol is already established by the project or the requested task is a small extension of the existing API style.

## 5. Contract-First Development

Define the API contract before or together with implementation.

For REST, define applicable:

- method;
- path;
- authentication;
- authorization expectations;
- path parameters;
- query parameters;
- request body;
- response body;
- status codes;
- error contract;
- idempotency behavior;
- retry behavior;
- pagination;
- side effects.

For message-driven APIs, define applicable:

- channel;
- direction;
- event or message type;
- payload;
- authentication;
- authorization;
- ordering;
- delivery semantics;
- retry behavior;
- reconnect behavior;
- versioning.

Do not implement a vague cross-service contract and force consumers to infer behavior from source code.

## 6. Approval for Material Contract Changes

Do not require separate approval for every small endpoint when the user already explicitly requested the feature and the contract is straightforward.

Obtain explicit approval before material changes such as:

- changing the public API architecture;
- changing protocol;
- changing authentication model;
- changing global error model;
- changing global pagination strategy;
- changing API namespace;
- introducing a breaking contract;
- introducing a new major API version.

## 7. Machine-Readable Source of Truth

### 7.1 REST / HTTP

Use OpenAPI as the machine-readable source of truth when the project and framework support it reliably.

Use the newest official version that is compatible with the project's tooling.

Do not force an OpenAPI upgrade merely because a newer specification exists.

### 7.2 GraphQL

The GraphQL schema is the source of truth for GraphQL types and operations.

### 7.3 Event and Message APIs

Use AsyncAPI when the message-driven API is complex enough that machine-readable documentation provides real value.

Do not require AsyncAPI for a very small WebSocket or event interface when it would add unnecessary maintenance overhead.

## 8. REST Resource Design

Prefer resource-oriented paths.

Good:

```text
/users
/subscriptions
/servers
/payments
```

Avoid unnecessary RPC-style paths such as:

```text
/getUsers
/createSubscription
/deleteServer
```

Domain commands are allowed when the operation is naturally an action rather than ordinary CRUD.

Example:

```text
POST /subscriptions/{id}/activate
```

Do not sacrifice domain clarity merely to make every operation look like pure CRUD.

## 9. HTTP Method Semantics

Use HTTP methods according to their real semantics.

Typical use:

```text
GET     read
POST    create or non-idempotent command
PUT     full replacement when appropriate
PATCH   partial modification
DELETE  removal
```

Do not mutate state through `GET`.

Do not introduce newer or less widely supported HTTP methods unless compatibility with the project's framework, clients, proxies, gateways, and documentation tooling is confirmed.

## 10. HTTP Status Codes

Use status codes according to the actual result.

Examples:

```text
200 OK
201 Created
202 Accepted
204 No Content

400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
422 Unprocessable Content
429 Too Many Requests

500 Internal Server Error
502 Bad Gateway
503 Service Unavailable
```

Do not routinely encode normal HTTP failures as:

```json
{
  "success": false,
  "error": "..."
}
```

under a `200 OK` response.

## 11. Error Contract

For HTTP APIs, prefer a consistent Problem Details-style error contract.

Support stable project-specific extensions when useful, such as:

- `code`;
- `field_errors`;
- `request_id`.

Example concept:

```json
{
  "type": "about:blank",
  "title": "Subscription not found",
  "status": 404,
  "detail": "No active subscription exists for the current user.",
  "code": "SUBSCRIPTION_NOT_FOUND"
}
```

The entire API should use one coherent error model unless a protocol requires different semantics.

## 12. Stable Machine Error Codes

Use stable machine-readable error codes for business decisions.

Examples:

```text
SUBSCRIPTION_NOT_FOUND
INVALID_REFRESH_TOKEN
PAYMENT_ALREADY_PROCESSED
```

Do not require clients to branch on human-readable text.

Bad:

```text
if error.detail == "Subscription was not found"
```

Human-readable messages may change or be localized.

Machine codes are part of the stable contract.

## 13. Success Response Shape

Do not wrap every successful response mechanically in a generic envelope such as:

```json
{
  "success": true,
  "data": {}
}
```

Return the actual resource or domain result unless a wrapper provides real contract value.

Collections may use structures such as:

```json
{
  "items": [],
  "pagination": {}
}
```

when needed.

## 14. Null vs Missing

Define the semantics of:

- a missing field;
- a field explicitly set to `null`.

Do not change between missing and `null` accidentally.

The contract must determine what each state means.

## 15. API Data Types

### 15.1 Timestamps

Use ISO 8601 / RFC 3339 timestamps with explicit timezone.

Example:

```text
2026-08-18T06:00:00+03:00
```

### 15.2 Enums

Prefer stable string enum values when they are part of the public contract.

Example:

```json
{
  "status": "active"
}
```

Do not expose meaningless internal numeric enum values unless the number itself is intentionally the public contract.

### 15.3 External IDs

External IDs must be:

- stable;
- opaque to consumers where practical;
- independent from internal database knowledge when possible.

Authorization must never depend on IDs being difficult to guess.

## 16. Money

Represent monetary values as decimal strings plus currency.

Preferred format:

```json
{
  "amount": "12.50",
  "currency": "EUR"
}
```

Do not use binary floating-point representation as the financial API contract.

Use the chosen monetary representation consistently.

## 17. Pagination

Choose pagination according to the data and access pattern.

### 17.1 Offset Pagination

Suitable for:

- admin tables;
- smaller datasets;
- use cases requiring arbitrary page navigation.

Example:

```text
?limit=50&offset=100
```

### 17.2 Cursor Pagination

Prefer for:

- large datasets;
- frequently changing collections;
- feeds;
- sequential traversal.

Example:

```text
?limit=50&cursor=...
```

Cursors must be opaque to clients.

Clients must not be required to construct or interpret cursor internals.

## 18. Filtering, Sorting, and Search

Use consistent naming across the API.

Example:

```text
?status=active
?sort=created_at
?order=desc
?search=helsinki
```

Avoid inconsistent alternatives for the same concept such as:

```text
sort_by
sortField
ordering
orderColumn
```

unless compatibility requires them.

Whitelist fields that may be used for filtering or sorting.

Do not pass arbitrary client-provided field names directly into database queries.

## 19. Collection Limits

Every collection endpoint must have bounded result size.

Define:

- default limit;
- maximum limit.

Choose values based on the real endpoint and workload.

Do not expose effectively unlimited result sizes.

## 20. Idempotency

Evaluate idempotency for operations where duplicate execution can create harmful side effects.

Examples:

- payments;
- subscription activation;
- order creation;
- external provisioning;
- resource creation triggered by unreliable networks.

Use an idempotency mechanism when needed.

Example:

```text
Idempotency-Key: ...
```

The same logical request with the same accepted idempotency key must not create duplicate side effects.

Do not assume `POST` is idempotent by default.

## 21. Retry Semantics

Define retry behavior for operations that clients, workers, gateways, or integrations may retry.

Determine:

- whether retry is safe;
- which failures are retryable;
- timeout behavior;
- backoff;
- duplicate protection;
- idempotency requirements.

Do not automatically retry mutating non-idempotent requests without a safe duplicate-control mechanism.

## 22. Optimistic Concurrency

When concurrent modification can cause lost updates, consider explicit concurrency control.

Possible mechanisms:

```text
ETag
If-Match
```

or a domain version field:

```json
{
  "version": 7
}
```

Do not add optimistic concurrency to every resource mechanically.

## 23. HTTP Caching

For read-heavy APIs, evaluate:

- `Cache-Control`;
- `ETag`;
- conditional requests;
- `Vary`.

Do not cache private or authenticated responses without understanding cache scope and privacy consequences.

Do not add caching unless consistency and invalidation semantics are understood.

## 24. API Versioning Strategy

For public REST APIs, prefer path-based major versioning:

```text
/api/v1/...
```

Internal APIs may remain unversioned when the project has no compatibility need and all consumers move together.

Do not introduce new major API versions for additive or internal-only changes.

## 25. Keep v1 as Long as Compatible

Keep `/api/v1` for as long as backward compatibility can reasonably be preserved.

The existence of new endpoints, new optional fields, new filters, new localization behavior, or other additive changes does not require `/v2`.

A new major API version is justified only when a necessary public contract change is materially incompatible with existing consumers and cannot be safely introduced within the current major version.

## 26. Major API Version Approval

Never increase the API major version automatically.

If a breaking change appears to require `/v2` or a later major version:

- explain why the change is breaking;
- explain why it cannot be safely introduced within the current major version;
- identify affected consumers;
- ask the user whether to introduce the new API major version.

The agent may proceed directly only when the user explicitly instructed that the project is moving to `/v2` or another specific major API version.

Do not confuse application release version changes with API major version changes.

## 27. Breaking Change Classification

Treat changes such as the following as breaking unless compatibility analysis proves otherwise:

- removing a field;
- renaming a field;
- changing a field type;
- changing a field's meaning;
- making an optional input required;
- changing nullability incompatibly;
- changing error semantics;
- changing authentication requirements;
- changing method;
- changing path;
- incompatibly changing pagination;
- incompatibly changing request or response structure.

Usually compatible changes include:

- adding a new endpoint;
- adding an optional response field;
- adding an optional request field;
- adding a backward-compatible capability.

### 27.1 Enum Expansion

A new enum value is potentially breaking because existing consumers may assume the old enum set is exhaustive.

Before treating a new enum value as backward-compatible, evaluate known consumers.

## 28. Major Version Migration

When a new major API version is approved:

```text
design replacement
→ introduce new major version
→ keep previous major available
→ mark previous contract deprecated
→ update documentation
→ migrate consumers
→ verify migration
→ remove previous major only after consumers no longer depend on it
```

Run old and new major versions in parallel when existing consumers still require the previous version.

Use cross-session synchronization for consumer migration when services are developed separately.

## 29. Deprecation

Do not remove an existing contract abruptly.

Use:

```text
introduce replacement
→ mark deprecated
→ update docs
→ update consumers
→ migration period
→ remove only in an approved breaking version
```

Track affected consumers explicitly.

## 30. GraphQL Schema Rules

For GraphQL:

- use the schema as the source of truth;
- define clear types;
- choose nullable and non-null deliberately;
- queries must not mutate state;
- mutations perform state changes;
- authorization remains server-side;
- evaluate N+1 behavior;
- use batching only when justified;
- keep pagination consistent;
- control arbitrary query complexity;
- deprecate fields before removal.

Do not make schema changes without evaluating compatibility.

## 31. GraphQL Transport and Errors

Distinguish:

- HTTP transport errors;
- GraphQL execution errors;
- application/business errors.

Use GraphQL's normal top-level execution result model where appropriate.

Provide stable application-level machine codes when clients must make business decisions.

For version- or transport-sensitive GraphQL behavior, verify current official specification and framework documentation.

## 32. WebSocket Contract

Define an application message contract.

Example:

```json
{
  "type": "subscription.updated",
  "id": "...",
  "data": {}
}
```

Add fields such as the following only when useful:

- `request_id`;
- `correlation_id`;
- `version`;
- `timestamp`.

Define:

- connection authentication;
- per-operation authorization;
- message types;
- message payload schemas;
- errors;
- heartbeat or ping behavior;
- reconnect behavior;
- timeouts;
- connection limits;
- message size limits;
- backpressure;
- graceful close.

An authenticated connection does not automatically authorize every operation sent over that connection.

## 33. SSE Contract

For SSE, define applicable:

- `event`;
- `id`;
- `data`;
- `retry`.

Also define:

- event names;
- reconnect behavior;
- `Last-Event-ID` handling;
- heartbeat or keepalive behavior;
- authorization;
- connection lifetime;
- resource limits.

## 34. Event Naming

Prefer stable domain-based names.

Good:

```text
subscription.created
subscription.updated
subscription.expired
payment.completed
server.status_changed
```

Avoid vague names such as:

```text
event1
update
changed
```

## 35. Event Versioning

Do not change event payloads incompatibly without a migration strategy.

Version may be represented at:

- message level;
- channel level;
- schema level.

Example when message-level versioning is useful:

```json
{
  "type": "subscription.updated",
  "version": 1,
  "data": {}
}
```

Do not add version fields mechanically when the channel or schema already provides a clear compatibility boundary.

## 36. Webhook Contract

Define:

- event type;
- event ID;
- timestamp;
- payload;
- signature or authentication method;
- delivery semantics;
- timeout;
- retry policy;
- duplicate handling;
- ordering guarantees.

Webhook consumers must assume duplicate delivery is possible unless the provider explicitly guarantees otherwise.

## 37. Webhook Verification

For third-party provider webhooks, implement verification according to the provider's official documentation.

For custom webhook protocols, use an authenticated integrity mechanism appropriate to the system.

Do not invent an ad hoc signature format when a standard or established provider mechanism already exists.

## 38. Webhook Replay Protection

Evaluate:

- signature;
- timestamp;
- event ID;
- acceptable time window;
- duplicate event detection.

Do not accept indefinitely replayable valid historical webhook messages when replay creates a security or business risk.

## 39. Webhook Delivery Semantics

Explicitly define whether delivery is:

- best effort;
- at least once;
- another documented model.

Unless a stronger guarantee exists, design consumers to tolerate:

```text
at-least-once delivery
+ duplicate events
+ no ordering assumption
```

Do not rely on event arrival order unless the provider or protocol guarantees it.

## 40. AsyncAPI

Use AsyncAPI for sufficiently complex:

- WebSocket APIs;
- event-driven APIs;
- pub/sub systems;
- multi-channel message systems.

Do not make AsyncAPI mandatory for trivial realtime interfaces.

Use the current official specification that is compatible with project tooling.

## 41. Baseline API Security

API Development must enforce baseline security during design and implementation.

Require applicable:

- server-side authentication;
- server-side authorization;
- explicit input validation;
- bounded collections;
- resource limits;
- webhook verification;
- replay protection;
- secret minimization;
- no stack trace exposure;
- TLS for external traffic;
- no trust in frontend restrictions;
- least-data exposure.

Use `Security Audit` for deep vulnerability analysis.

## 42. Sensitive Data Minimization

Return only fields needed by the consumer.

Do not design responses as:

```text
SELECT * → serialize database model
```

Response schemas are API contracts, not database mirrors.

A new database field must not automatically become a new API field.

## 43. Mass Assignment Protection

Define explicit request schemas.

Do not bind arbitrary client payloads directly to internal models when the internal model contains protected fields.

Example:

Client may update:

```text
display_name
language
```

but must not implicitly gain control over:

```text
is_admin
role
balance
owner_id
```

## 44. Observability

For API flows, support correlation between relevant:

- request;
- logs;
- errors;
- external calls.

Use request or correlation IDs when useful.

Do not expose sensitive internal tracing information to public consumers.

## 45. Positive and Negative API Tests

For every new or materially changed contract, test applicable:

### Positive

- valid request;
- expected response;
- expected side effect.

### Validation

- missing required field;
- wrong type;
- boundary value;
- invalid enum;
- invalid format.

### Authentication

- missing credential;
- invalid credential;
- expired credential.

### Authorization

- cross-user access;
- wrong role;
- protected operation.

### Error Contract

- correct status;
- correct machine code;
- correct schema.

### Compatibility

- previous consumer expectations where applicable.

## 46. Contract Tests

When multiple services depend on the same contract, use contract validation where practical.

Examples:

- validate backend responses against OpenAPI;
- validate messages against schemas;
- use consumer/provider contract tests for important service boundaries.

Do not assume provider tests alone prove consumer compatibility.

## 47. Documentation Synchronization

After API changes, check and update applicable:

### OpenAPI / GraphQL Schema / AsyncAPI

Keep the detailed machine-readable contract synchronized with implementation.

### README

Update the concise API catalog.

### AGENT

Update only when an important project-specific architectural or compatibility fact changed.

### `*_SYNC.md`

Create or update cross-session tasks when another service must adapt.

## 48. Public Changelog and Devlog

If the API work creates a real user-visible effect, apply the public changelog/devlog rules.

Do not publish internal API details such as:

```text
Added POST /api/v1/subscriptions
```

when the user-facing effect can be described instead.

## 49. Verification Workflow

After implementation:

```text
schema verification
→ targeted tests
→ negative tests
→ contract tests where applicable
→ Docker runtime verification
→ log inspection
→ consumer compatibility check
→ documentation synchronization
```

When generated API documentation exists, verify that it matches the actual implementation.

## 50. Definition of Done

API work is complete only when all applicable conditions are satisfied:

- the correct protocol was selected;
- material protocol choices were approved when required;
- the contract was defined before or together with implementation;
- breaking impact was evaluated;
- naming is consistent;
- HTTP methods follow real semantics;
- status codes are appropriate;
- the error contract is consistent;
- machine-readable error codes are stable where needed;
- successful responses avoid unnecessary generic envelopes;
- nullability semantics are intentional;
- timestamps use explicit timezone;
- enum values are stable;
- external IDs are treated as opaque identifiers where appropriate;
- money uses decimal strings plus currency;
- pagination is appropriate for the dataset;
- collection sizes are bounded;
- filtering and sorting fields are controlled;
- idempotency was considered;
- retry behavior was considered;
- concurrency protection was considered where applicable;
- caching was considered where applicable;
- `/api/v1` was preserved when backward compatibility remained possible;
- a new API major version was not introduced without explicit user instruction or approval;
- breaking changes have an approved migration strategy;
- enum expansion was evaluated against known consumers;
- deprecated contracts were not removed prematurely;
- GraphQL schema and nullability are intentional where applicable;
- GraphQL authorization remains server-side;
- WebSocket auth, authorization, reconnect, error, and limit semantics are defined where applicable;
- SSE reconnect and event ID behavior are defined where applicable;
- event names are stable and domain-based;
- event payload compatibility is preserved;
- webhook signature verification is defined where applicable;
- webhook replay protection is defined where applicable;
- webhook consumers tolerate duplicates when required;
- delivery and ordering semantics are documented;
- sensitive data exposure is minimized;
- request schemas protect internal fields from mass assignment;
- baseline API security requirements are satisfied;
- positive tests passed;
- validation tests passed;
- authentication and authorization negative tests passed where applicable;
- error-contract tests passed;
- contract tests passed where applicable;
- Docker runtime was verified;
- logs were checked;
- machine-readable API documentation was synchronized;
- README was updated where required;
- AGENT was checked and updated where required;
- cross-session synchronization files were updated where required;
- public changelog/devlog impact was checked.

If an applicable verification cannot be completed, mark it explicitly as unverified rather than claiming the API contract is fully validated.
