---
name: Security Audit
description: >
  Governs explicitly requested deep security audits of project applications,
  repositories, project configuration, and project-owned runtime components,
  including scope approval, audit levels, threat modeling,
  architecture and attack-surface mapping, authentication and authorization
  review, API and business-logic testing, secrets and supply-chain review,
  Docker and CI/CD security, evidence-based findings, remediation approval,
  re-testing, regression protection, and local security reporting.
---

# Security Audit

## 1. Purpose

Use this skill only when the user explicitly requests a security audit, security review, vulnerability assessment, re-audit, pre-production security gate, or server infrastructure security audit.

The goal is to perform evidence-based security analysis without silently modifying the project, overstating unverified risks, installing tools without approval, or expanding the requested scope without consent.

This skill is for deep security work.

Routine development-time security checks remain governed by the normal development skills and do not automatically activate this skill.

## 2. Activation

Activate this skill only on explicit user request.

Examples:

- perform a security audit;
- review authentication security;
- audit API authorization;
- check the project before production;
- re-test previously fixed vulnerabilities;
- audit the server configuration;
- audit Docker and deployment security.

If the user requests a security audit but does not specify the audit mode or level, ask which mode and level should be used before proceeding.

Do not assume `Full Audit` or the deepest level automatically.

## 3. Audit Modes

Supported modes:

### 3.1 Full Audit

Review the complete in-scope application security posture.

Typical areas include:

- architecture;
- authentication;
- authorization;
- input handling;
- API security;
- business logic;
- secrets;
- dependencies;
- Docker;
- CI/CD;
- logging;
- data security;
- cryptography;
- external integrations;
- runtime configuration.

### 3.2 Scoped Audit

Audit only the explicitly selected area.

Examples:

- authentication;
- authorization;
- API security;
- secrets;
- Docker;
- frontend security;
- dependency security;
- business logic.

Include directly related dependencies and trust boundaries when necessary to assess the requested scope correctly.

### 3.3 Re-Audit

Re-test findings from a previous audit.

Workflow:

```text
previous finding
→ reproduce original condition
→ verify remediation
→ test likely bypasses
→ run regression checks
→ update status
```

### 3.4 Pre-Production Audit

Perform a deep production-readiness security review.

This mode includes a production security gate defined later in this skill.

## 4. Audit Levels

Use one of the following depth levels.

### 4.1 Level 1 — Quick Review

Use for focused, time-efficient review of high-risk issues in a narrow scope.

Prioritize:

- obvious authentication or authorization flaws;
- exposed secrets;
- unsafe configuration;
- high-impact input handling;
- dangerous project runtime or configuration exposure;
- known vulnerable dependencies directly relevant to the scope.

### 4.2 Level 2 — Deep Audit

Use as the standard thorough security audit.

Includes:

- architecture review;
- threat modeling;
- manual reasoning;
- negative testing;
- relevant automated checks;
- abuse-case analysis;
- evidence-backed findings;
- remediation planning.

### 4.3 Level 3 — Exhaustive / Pre-Production

Use for the deepest practical review before production or for explicitly requested exhaustive audits.

Includes all relevant Level 2 work plus broader verification of:

- project runtime exposure;
- project container and deployment configuration;
- supply chain;
- CI/CD;
- production-facing project configuration;
- project-controlled trust boundaries;
- residual risk;
- production security gate.

If the user does not specify a level, ask which level to use.

## 5. Two Approval Gates

Use two distinct approval gates.

### 5.1 Approval Gate 1 — Audit Plan

Before active security testing or intrusive analysis:

1. inspect the system using safe read-only methods;
2. define scope;
3. identify the environment;
4. define the audit level;
5. map initial components and trust boundaries;
6. prepare the audit plan;
7. present the plan to the user;
8. wait for approval.

The user may approve the plan:

- fully;
- partially;
- by selected sections.

Approval to perform the audit does not authorize remediation.

### 5.2 Approval Gate 2 — Remediation Plan

After the audit:

1. present findings;
2. classify severity and confidence;
3. prepare the remediation plan;
4. explain material architecture or compatibility impact;
5. wait for explicit user approval before modifying project code, project configuration, project-owned runtime components, or repository state.

Only approved remediation work may proceed.

## 6. Project Scope Boundary, Authorization, and Environment Safety

This skill is strictly limited to the project and project-controlled security surface.

Allowed scope includes:

- project source code;
- project repository and relevant Git history;
- project configuration files;
- project environment-variable definitions and validation logic;
- project Dockerfiles and Compose files;
- project-owned containers and services;
- project application runtime behavior;
- project API and inter-service contracts;
- project CI/CD configuration;
- project dependencies and supply chain;
- project-controlled logs and error behavior;
- project-owned external integrations and endpoints when needed to assess the application contract.

This skill does not audit or modify the host machine outside the project scope.

Do not inspect or change host-level areas such as:

- operating-system hardening;
- SSH daemon configuration;
- host firewall configuration;
- host users or groups;
- sudo configuration;
- system-wide filesystem permissions;
- systemd units outside the project;
- cron jobs outside the project;
- host package configuration;
- Docker daemon configuration;
- host-wide Docker networking;
- reverse-proxy configuration maintained outside the project;
- host database or cache service configuration maintained outside the project;
- host backup system configuration;
- unrelated services running on the machine.

If project configuration references a host path, Docker socket, host network mode, privileged container mode, or another host-level capability, evaluate the security risk of that declaration from the project side, but do not inspect or audit the underlying host resource itself.

Host-machine and server-infrastructure security requires a separate explicitly requested skill and is out of scope here.

Before testing, identify:

- target project or service;
- target application environment;
- project-controlled domains or endpoints;
- external dependencies relevant to the project;
- allowed actions;
- prohibited actions.

Prefer active testing against:

- local;
- development;
- staging.

Do not perform destructive or availability-impacting testing against production without explicit authorization.

Do not automatically perform:

- brute force;
- credential spraying;
- denial-of-service;
- load saturation;
- destructive database operations;
- mass enumeration;
- destructive project-file operations.

Production testing should default to non-destructive verification.

## 7. Baseline and Security Benchmark

Before the audit:

- verify current Docker/service startup state;
- run relevant existing tests;
- inspect current logs;
- record existing errors and warnings;
- identify project-exposed services and ports;
- identify authentication methods;
- identify privileged paths;
- identify external integrations;
- identify data stores;
- identify secret sources;
- record relevant security baseline data.

When the audit includes performance- or resource-sensitive security controls, perform a real baseline benchmark where appropriate.

Examples:

- rate-limit behavior;
- authentication request cost;
- resource consumption for expensive endpoints;
- file-upload limits;
- connection limits;
- container resource usage.

Do not invent benchmark results.

## 8. Architecture and Asset Mapping

Before finding vulnerabilities, map:

- actors;
- assets;
- entry points;
- trust boundaries;
- data flows;
- privileged operations;
- external services;
- storage systems;
- administrative surfaces.

For each important data flow, determine:

- who initiates it;
- how identity is established;
- where authorization is enforced;
- what data crosses the boundary;
- what happens if input is malicious;
- what component owns the decision.

## 9. Threat Modeling

Threat modeling is required for Level 2 and Level 3 audits unless the selected scope makes it unnecessary.

Ask:

- what must be protected;
- who can attack it;
- what entry points exist;
- what trust assumptions exist;
- what the attacker gains;
- what control prevents the attack;
- whether that control can be bypassed.

Consider actors such as:

- unauthenticated user;
- authenticated user;
- user attacking another user's resources;
- administrator;
- compromised integration;
- malicious webhook sender;
- compromised frontend;
- compromised container;
- compromised service account.

## 10. Authentication Audit

Review applicable authentication flows:

- login;
- registration;
- password reset;
- email verification;
- Telegram or external identity verification;
- MFA;
- session creation;
- session expiration;
- logout;
- refresh tokens;
- token rotation;
- revocation;
- remember-me flows;
- account recovery.

Test relevant negative scenarios:

- expired token;
- revoked token;
- reused refresh token;
- stolen refresh token;
- disabled account;
- deleted account;
- password change;
- concurrent sessions;
- malformed credentials;
- replayed authentication artifacts.

## 11. Authorization Audit

For each protected operation, verify:

- anonymous access;
- cross-user access;
- object ownership;
- role boundaries;
- admin-only functionality;
- direct API access independent of frontend restrictions;
- function-level authorization;
- property-level authorization;
- object-level authorization.

Ask:

```text
Can user A act on user B's resource?
Can a normal user call an admin operation?
Can an object identifier be replaced?
Can hidden or protected fields be modified?
Can the endpoint be called directly when the UI hides the action?
```

Never treat frontend visibility or client-side validation as an authorization control.

## 12. Input and Injection Audit

Review untrusted inputs including:

- query parameters;
- path parameters;
- JSON bodies;
- forms;
- headers;
- cookies;
- webhooks;
- file metadata;
- file contents;
- third-party API responses;
- externally derived configuration.

Check applicable risks:

- SQL injection;
- command injection;
- template injection;
- path traversal;
- XSS;
- header injection;
- query-language injection;
- unsafe deserialization;
- parser abuse.

Do not report a confirmed injection issue without understanding the real data flow and exploitability.

## 13. API Security Audit

Review applicable API risks including:

- broken object-level authorization;
- broken authentication;
- broken object property-level authorization;
- unrestricted resource consumption;
- broken function-level authorization;
- unrestricted access to sensitive business flows;
- SSRF;
- security misconfiguration;
- improper API inventory;
- unsafe consumption of third-party APIs.

Security Audit evaluates the security of API behavior.

API protocol design and contract design belong to the dedicated API development process.

## 14. Business Logic and Abuse Cases

Review legitimate features for abusive usage.

Examples:

- repeated trial activation;
- payment callback replay;
- subscription manipulation;
- coupon reuse;
- bypassing required workflow steps;
- unlimited expensive operations;
- replay of sensitive actions;
- race conditions affecting business state;
- abusing valid endpoints in an unsafe sequence.

Do not rely only on scanner results for business-logic security.

## 15. Resource Abuse and Rate Limits

Review operations that may require abuse protection:

- login;
- password reset;
- OTP;
- registration;
- search;
- file upload;
- expensive database queries;
- external API calls;
- Telegram actions;
- subscription activation;
- report generation;
- resource-intensive background work.

Evaluate:

- cost;
- abuse potential;
- user impact;
- external provider limits;
- system capacity;
- existing throttling.

Do not apply one generic rate limit to every operation without justification.

## 16. SSRF and External Request Security

Review backend-controlled network requests.

Check:

- user-controlled URLs;
- localhost access;
- private network access;
- metadata services;
- redirects;
- DNS behavior;
- allowed protocols;
- hostname validation;
- outbound network restrictions.

Include external integrations, webhook callbacks, remote nodes, and user-supplied URLs when applicable.

## 17. Frontend and Browser Security

Review applicable frontend risks:

- XSS;
- unsafe HTML rendering;
- token storage;
- CSRF;
- CORS assumptions;
- CSP;
- open redirects;
- sensitive browser storage;
- source maps;
- debug information;
- exposed internal metadata.

Client-side validation is not a security boundary.

## 18. File and Upload Security

When file handling exists, review:

- type validation;
- size limits;
- filename handling;
- path traversal;
- overwrite behavior;
- content sniffing;
- malware exposure;
- storage permissions;
- public accessibility;
- file lifecycle;
- temporary file handling.

## 19. Secrets and Configuration

Review:

- `.env`;
- Docker Compose;
- Dockerfile;
- source code;
- Git history when relevant;
- logs;
- exceptions;
- CI/CD;
- backups;
- configuration files;
- mounted secrets.

Look for:

- API keys;
- database passwords;
- JWT secrets;
- bot tokens;
- private keys;
- hard-coded credentials;
- unsafe defaults;
- reused dev/prod credentials.

Also review:

- rotation capability;
- least privilege;
- scope;
- expiration;
- environment separation;
- accidental disclosure paths.

Never copy real secrets into the audit report or chat.

## 20. Cryptography and Transport Security

Review applicable:

- TLS;
- certificate validation;
- password hashing;
- token signing;
- random generation;
- encryption;
- key handling;
- key rotation;
- cryptographic library usage.

Do not create custom cryptographic algorithms or replace established cryptographic mechanisms with ad hoc implementations.

## 21. Data Security

Review:

- sensitive data inventory;
- storage location;
- read access;
- write access;
- retention;
- deletion;
- logging;
- backups;
- API overexposure;
- export behavior.

Pay special attention to:

- password hashes;
- tokens;
- personal data;
- billing information;
- VPN credentials or configuration;
- administrator data;
- authentication artifacts.

## 22. Dependencies and Supply Chain

Review:

- direct dependencies;
- transitive dependencies;
- lockfiles;
- vulnerable versions;
- unexpected packages;
- package installation scripts;
- package integrity;
- Docker base images;
- build dependencies;
- pinned versions;
- dependency provenance where practical.

Do not automatically upgrade major dependencies during the audit.

Dependency remediation follows the normal approval gate.

## 23. Docker and Project Runtime Security

Review project Dockerfiles, Compose configuration, and project-owned containers only.

Do not audit Docker daemon configuration, unrelated containers, host networking, or host filesystem contents outside the project.

Review applicable:

- non-root execution;
- Linux capabilities;
- privileged mode;
- host mounts declared by the project, without inspecting the mounted host path itself;
- Docker socket access declared by the project, without auditing the host Docker daemon;
- secrets in image layers;
- published ports;
- project container network isolation;
- read-only filesystem where practical;
- image provenance;
- unused packages;
- runtime permissions;
- resource limits.

## 24. CI/CD and Repository Security

Review applicable:

- CI secrets;
- workflow permissions;
- untrusted PR execution;
- third-party actions;
- action version pinning;
- deployment credentials;
- artifacts;
- release process;
- environment boundaries;
- deployment permissions.

Do not modify GitHub settings, repository access, actions configuration, or secrets without explicit approval under the repository-management rules.

## 25. Logging and Error Handling

Verify that security-relevant events are logged appropriately.

Examples:

- login failures;
- authorization failures;
- account security changes;
- administrator actions;
- security-sensitive operations.

Do not log:

- passwords;
- access tokens;
- refresh tokens;
- private keys;
- full secrets.

Do not expose detailed stack traces, database internals, or sensitive implementation details to end users.

## 26. Security Toolchain Policy

### 26.1 No Unapproved Installation

Do not install third-party security software on the host without explicit user approval. Tool installation permission does not expand the audit scope to host-machine security.

### 26.2 Tool Priority

Use this order:

```text
already available trusted tool
→ official or trusted Docker image
→ temporary Docker container
→ host installation only with explicit approval
```

Prefer disposable Docker-based tooling so the environment can be cleaned easily after the audit.

### 26.3 Docker Tool Safety

Before using a security-tool container:

- prefer official or vendor-maintained images;
- avoid untrusted community images without validation;
- mount the project read-only when write access is unnecessary;
- avoid mounting sensitive host paths unnecessarily;
- do not expose Docker socket unless strictly required and explicitly approved;
- remove temporary containers when no longer needed.

### 26.4 Tool Proposal

If a new tool is useful, explain:

- tool name;
- purpose;
- why it is needed;
- Docker or installation method;
- required permissions;
- expected output.

Then request explicit approval before introducing it.

## 27. Automated Security Checks

Automated tools are evidence sources, not final truth.

A scanner finding does not automatically equal a confirmed vulnerability.

For each relevant scanner result:

```text
scanner signal
→ inspect code/configuration
→ understand data flow
→ assess exploitability
→ reproduce safely when practical
→ determine impact
→ classify finding
```

A clean scanner result does not prove the application is secure.

Manual reasoning remains required for:

- authorization;
- business logic;
- workflow abuse;
- trust boundaries;
- architecture-specific risks.

## 28. Manual Verification

Use manual analysis to verify:

- business logic;
- privilege boundaries;
- object ownership;
- authentication state transitions;
- data-flow assumptions;
- cross-service trust;
- misuse scenarios;
- bypass opportunities.

Do not rely solely on static analysis, dependency scanners, or DAST.

## 29. Evidence and Confidence

Assign a confidence level to every finding.

Allowed values:

- `Confirmed`;
- `Likely`;
- `Potential`;
- `Not Reproducible`;
- `Unverified`.

### Confirmed

Use when there is strong evidence such as:

- reproducible runtime behavior;
- failing security test;
- safe proof of concept;
- direct code and configuration evidence with clear exploitability.

### Likely

Use when evidence is strong but complete runtime reproduction is unavailable.

### Potential

Use when a risky pattern exists but exploitability is not proven.

Do not present `Potential` findings as confirmed vulnerabilities.

## 30. Risk Severity

Classify severity separately from confidence.

Use:

- `Critical`;
- `High`;
- `Medium`;
- `Low`;
- `Informational`.

Consider:

- exploitability;
- required privileges;
- exposure;
- confidentiality impact;
- integrity impact;
- availability impact;
- affected data;
- blast radius;
- prerequisites;
- business impact.

Example:

```text
Severity: High
Confidence: Confirmed
```

Do not present a theoretical critical impact as a confirmed Critical vulnerability when exploitability is only potential.

## 31. CWE and CVSS

When the vulnerability type is clear, include the relevant CWE identifier when practical.

Example:

```text
CWE: CWE-89
```

CVSS is optional by default.

Use CVSS when:

- the user requests it;
- a formal client-facing or compliance-style report requires it;
- standardized scoring materially improves the report.

Do not use CVSS to create false precision when evidence is incomplete.

## 32. Finding Format

Use a consistent structure.

```text
ID: SEC-001
Title:
Severity:
Confidence:
CWE:
CVSS: optional
Area:
Affected component:

Problem:
Evidence:
Attack scenario:
Impact:
Preconditions:
Existing protections:
Recommended remediation:
Verification:
References:
```

Do not include exploit-enabling details in the chat summary.

Sensitive technical evidence may remain only in the local audit report when appropriate.

## 33. Critical Finding Handling

If a genuinely critical issue is discovered, report it to the user immediately instead of waiting for the entire audit to finish.

Examples:

- exposed active credentials;
- authentication bypass;
- remote code execution;
- mass data exposure;
- destructive authorization bypass.

Report:

- affected area;
- risk;
- whether immediate containment is required.

Do not expose the secret or exploit payload in chat.

If credentials are compromised, prioritize:

```text
revoke / rotate
→ restore safe operation
→ clean history or evidence paths afterward
```

## 34. Remediation Plan

After the audit, prepare a remediation plan before changing the system.

The remediation plan should include:

- finding ID;
- proposed fix;
- affected components;
- architecture impact;
- compatibility impact;
- migration impact;
- expected risk reduction;
- verification method;
- recommended order.

Use severity and technical dependency together to determine implementation order.

Do not assume strict severity order when a foundational fix safely resolves multiple higher-level symptoms.

## 35. Remediation Approval

Do not modify code, infrastructure, configuration, Docker, CI/CD, repository settings, or secrets until Approval Gate 2 is satisfied.

The user may approve:

- the entire remediation plan;
- selected findings;
- selected implementation phases.

Unapproved findings remain documented but unchanged.

## 36. Remediation Workflow

For each approved finding:

```text
prepare test or reproduction
→ implement remediation
→ run focused tests
→ run security regression test
→ attempt original attack
→ attempt likely bypasses
→ verify normal behavior still works
→ update finding status
```

Use logical local commits for completed remediation work under the repository-management rules.

## 37. Security Regression Tests

When practical, every fixed vulnerability should receive a regression test.

Example:

```text
user A requests user B resource
→ expected: denied
```

Security regression tests should protect the fixed behavior from returning later.

## 38. Re-Test

A finding is not `Fixed and Verified` merely because code changed.

Re-test:

- the original attack path;
- legitimate expected behavior;
- likely bypass variants;
- related negative cases;
- regression suite.

Only then mark the finding as verified.

## 39. Finding Status

Use applicable final statuses:

- `Fixed and Verified`;
- `Mitigated`;
- `Accepted Risk`;
- `Deferred`;
- `Not Reproducible`;
- `Unverified`.

`Accepted Risk` requires an explicit user decision.

The agent must not accept risk on the user's behalf.

## 40. Pre-Production Security Gate

Use only in Pre-Production Audit mode.

Evaluate whether unresolved security issues should block production release.

Check applicable project items such as:

- no unresolved Critical findings;
- unresolved High findings explicitly reviewed;
- project production secrets configured securely;
- debug behavior disabled;
- authorization tests passing;
- authentication controls verified;
- dependencies reviewed;
- project Docker/runtime security reviewed;
- project-published ports reviewed;
- project-controlled deployment configuration reviewed;
- project administrative endpoints reviewed when applicable;
- production-facing project configuration reviewed;
- CI/CD deployment permissions reviewed.

Host-machine hardening, SSH, firewall, system services, host backup configuration, and other server-wide controls are explicitly outside this gate.

If a Critical issue remains unresolved, report production as blocked from a security perspective.

For unresolved High findings, report the risk and require explicit user decision before treating release as acceptable.

Do not accept production risk automatically.

## 41. Documentation and Synchronization

After approved remediation, check whether security changes require updates to:

- `README.md`;
- `AGENT.md`;
- relevant `*_SYNC.md`.

Examples:

- new security-related public workflow → README;
- new project-specific security invariant → AGENT;
- remediation requires work from another service → appropriate sync file.

Do not place exploit details or sensitive evidence in public project documentation.

## 42. Security Audit Report

Create a local Markdown report:

```text
SECURITY_AUDIT.md
```

The report must be written entirely in English.

Do not commit or push this report to Git or GitHub.

Ensure the report is excluded from Git tracking.

A suitable ignore rule is:

```gitignore
SECURITY_AUDIT.md
```

The report may contain sensitive security details and is treated as a local audit artifact.

Recommended structure:

```text
# Security Audit

## Executive Summary
## Scope
## Audit Mode and Level
## Application Environment
## Methodology
## Architecture and Assets
## Attack Surface
## Threat Model
## Findings
## Remediation Plan
## Re-Test Results
## Residual Risk
## Accepted Risks
## Deferred Findings
## Unverified Areas
```

Update the same report through remediation and re-test when practical instead of creating unnecessary duplicate audit files.

## 43. Chat Summary

Provide the user with a concise Russian summary.

Example:

```text
Аудит завершён.

Найдено:
Critical: 0
High: 2
Medium: 4
Low: 3

Подтверждено: 6
Potential: 3

Основные риски:
- ...
- ...

Полный отчёт сохранён в SECURITY_AUDIT.md.
```

Do not expose unnecessary exploit details, credentials, or sensitive payloads in the chat summary.

## 44. Final Verification

After remediation and re-testing:

- re-run relevant application tests;
- re-run relevant security tests;
- verify Docker/service startup;
- inspect logs;
- repeat relevant security benchmarks;
- verify fixed findings;
- verify likely bypasses;
- review residual risk;
- review deferred findings;
- review unverified areas;
- update `SECURITY_AUDIT.md`;
- update documentation and sync files when required.

## 45. Definition of Done

A Security Audit is complete only when all applicable conditions are satisfied:

- the audit was explicitly requested;
- audit mode was selected;
- audit level was selected;
- scope was defined;
- audit activity remained inside the project security boundary and did not audit the host machine;
- project target environment and authorization scope were understood;
- safe project-scoped read-only discovery was performed before intrusive checks;
- baseline was recorded;
- relevant security benchmark data was captured where applicable;
- architecture and assets were mapped;
- attack surface was identified;
- threat model was completed where applicable;
- Audit Plan was presented;
- Approval Gate 1 was satisfied;
- only approved audit sections were executed;
- authentication was reviewed when applicable;
- authorization was reviewed when applicable;
- input and injection risks were reviewed when applicable;
- API security was reviewed when applicable;
- business-logic abuse was reviewed when applicable;
- resource abuse was reviewed when applicable;
- SSRF and outbound requests were reviewed when applicable;
- frontend security was reviewed when applicable;
- file handling was reviewed when applicable;
- secrets and configuration were reviewed when applicable;
- cryptography and transport were reviewed when applicable;
- data security was reviewed when applicable;
- dependencies and supply chain were reviewed when applicable;
- project Docker/runtime security was reviewed when applicable;
- CI/CD and repository security were reviewed when applicable;
- logging and error handling were reviewed when applicable;
- third-party tools were not installed without approval;
- Docker-based tooling was preferred when new tools were required;
- scanner findings were manually validated;
- every finding has evidence;
- every finding has severity;
- every finding has confidence;
- CWE was included where useful;
- CVSS was used only when appropriate;
- Critical findings were reported immediately;
- remediation plan was created;
- Approval Gate 2 was satisfied before modifications;
- only approved remediation was implemented;
- security regression tests were added where practical;
- fixed findings were re-tested;
- likely bypasses were checked;
- unresolved risk was reported;
- Accepted Risk statuses reflect explicit user decisions;
- Pre-Production Gate was evaluated when applicable;
- `SECURITY_AUDIT.md` was created or updated;
- the audit report remains local and outside Git;
- the audit report is written in English;
- the chat summary is written in Russian;
- project documentation was updated when required;
- cross-session dependencies were synchronized when required;
- unverified areas are explicitly listed.

If any applicable verification cannot be completed, mark it explicitly as `Unverified` rather than treating the audit as fully confirmed.
