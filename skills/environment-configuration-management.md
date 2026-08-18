---
name: Environment Configuration Management
description: >
  Governs the creation, editing, validation, naming, security, organization,
  synchronization, and runtime use of environment variables, .env files,
  .env.example files, Docker Compose configuration, and service configuration.
---

# Environment Configuration Management

## 1. Purpose

Use this skill as the primary standard for managing application and service configuration through environment variables, `.env` files, `.env.example`, Docker Compose, and related service configuration.

The goal is to keep configuration predictable, secure, readable, easy to validate, safe to share, and consistent across development and deployment environments.

## 2. Activation

Apply this skill when a task involves one or more of the following:

- creating, reading, or editing `.env`;
- creating, reading, or editing `.env.example`;
- adding, changing, renaming, or removing environment variables;
- Docker Compose environment configuration;
- service configuration files that depend on environment variables;
- naming Docker services, databases, containers, or logical infrastructure components;
- startup configuration validation;
- secrets or credentials passed through environment variables;
- configuration-related startup failures;
- synchronization between runtime configuration and example configuration.

## 3. Core Configuration Principles

Configuration must be:

- explicit;
- predictable;
- validated;
- safe to expose when intended for examples;
- separated from source code where environment-specific values are involved;
- readable by both developers and automated tooling.

Do not silently introduce configuration behavior that cannot be discovered from the validation schema, `.env.example`, or the project's dedicated documentation.

## 4. `.env` File Structure and Design

### 4.1 Logical Section Order

Organize `.env` and `.env.example` into clear logical sections.

Order sections by operational importance and dependency flow rather than alphabetically.

Use the following default order when applicable:

1. application identity and runtime;
2. network and ports;
3. database;
4. cache and queues;
5. authentication and authorization;
6. external integrations;
7. background jobs and workers;
8. storage and files;
9. backups and maintenance;
10. observability and logging;
11. optional feature-specific configuration.

Example:

```env
# Application

APP_HOST=0.0.0.0
APP_PORT=8000

# Database

DATABASE_HOST=postgres
DATABASE_PORT=5432
DATABASE_NAME=app

# Authentication

JWT_SECRET=<REQUIRED>

# Backups

BACKUP_ENABLED=true
BACKUP_INTERVAL_HOURS=24

# Observability

LOG_LEVEL=INFO
```

If the project already has a clear and consistent section order, preserve it unless there is a strong maintainability reason to reorganize it.

### 4.2 Visual Separators

Separate thematic configuration categories using comment headers.

Comments in configuration files must be written exclusively in English.

Use short technical section titles without decorative prose.

### 4.3 Variable Explanations

When a variable requires clarification about:

- format;
- units;
- allowed values;
- accepted range;
- required structure;

place a concise technical comment directly above that variable.

Example:

```env
# Timeout in seconds. Allowed range: 1-300.
REQUEST_TIMEOUT=30
```

Do not add explanatory comments when the variable name and value are already self-explanatory.

### 4.4 Technical Writing Style

Configuration comments must use concise technical language.

Avoid introductions, conversational phrasing, unnecessary explanation, and narrative text.

### 4.5 Quoting and Parsing

Do not apply quotes mechanically to all values containing special characters.

Use quoting and escaping according to the syntax and parser semantics of the actual consumer, such as Docker Compose, dotenv libraries, shell parsing, or framework-specific configuration loaders.

Quote values when necessary to preserve their exact meaning.

Pay special attention to values containing:

- spaces;
- `#`;
- `$`;
- quotes;
- backslashes;
- URLs with query strings;
- JSON;
- multiline text;
- certificates;
- PEM keys;
- other parser-sensitive characters.

When parser behavior is version-specific or ambiguous, verify it against official documentation before relying on it.

## 5. `.env.example` Synchronization

### 5.1 Structural Synchronization

When environment variables are added, renamed, changed, or removed, update `.env.example` in the same task.

`.env.example` must reflect:

- the same logical section structure;
- the same configuration variable names that a user is expected to configure;
- relevant technical comments;
- safe default values where defaults are intentional;
- placeholders where real values must not be exposed.

Do not copy actual secret values from `.env` into `.env.example`.

### 5.2 Safe Defaults

If a variable has an intentional and safe default, place that value in `.env.example`.

Example:

```env
APP_PORT=8000
```

A variable with a safe fallback may be omitted from the real `.env` if the application validation layer explicitly provides the same default.

The default defined in runtime validation and the value shown in `.env.example` must remain consistent.

### 5.3 Required Values and Placeholders

For required values that cannot have a safe default, use a clear placeholder.

Example:

```env
TELEGRAM_BOT_TOKEN=<REQUIRED>
DATABASE_PASSWORD=<REQUIRED>
```

A placeholder must never be treated as a valid runtime value.

The startup validation layer must reject missing, empty, malformed, or unchanged placeholder values when the variable is required.

### 5.4 Optional Values

Optional values may use an empty value only when the application explicitly treats an empty value as valid absence.

Example:

```env
SENTRY_DSN=
```

Do not use empty values for required secrets or required runtime settings.

## 6. Configuration as a Source of Truth

The runtime validation schema is the primary source of truth for:

- required versus optional configuration;
- default values;
- data types;
- enums;
- ranges;
- validation rules;
- relationships between configuration values.

`.env.example` is the human-readable configuration template and must remain consistent with that schema.

Do not duplicate complex validation logic in comments when the validation layer already expresses it clearly.

Dedicated project documentation may explain higher-level configuration behavior, workflows, or operational decisions.

## 7. Startup Validation and Fail-Fast Behavior

### 7.1 Language-Agnostic Validation

Every application service must validate its environment configuration during startup using the standard facilities or reliable validation libraries of its language and framework.

This requirement is language-agnostic.

For the primary supported stacks:

- TypeScript should use typed schema validation appropriate to the project;
- Python should use typed settings or schema validation appropriate to the project.

Other languages must implement equivalent startup validation.

### 7.2 Validation Scope

Validate all applicable properties, including:

- required values;
- optional values;
- types;
- booleans;
- integers and floating-point values;
- positive or negative constraints;
- port ranges;
- URLs;
- hostnames;
- enums;
- minimum and maximum ranges;
- empty strings;
- placeholder values;
- dependent configuration.

Do not rely on implicit string coercion when it can produce ambiguous behavior.

For example, the string `"false"` must not accidentally behave as a truthy boolean.

### 7.3 Fail-Fast

If a required variable is:

- missing;
- empty when empty is invalid;
- malformed;
- outside an allowed range;
- left as a placeholder;
- incompatible with another required setting;

the application must stop during startup and write a clear configuration error to the logs.

Do not allow the application to continue in a partially configured state when the missing configuration is required for correct operation.

### 7.4 Safe Defaults

Defaults are allowed only when they are intentional, safe, documented by the validation schema, and appropriate for all expected environments.

Do not use hidden fallback values for secrets or security-sensitive configuration.

Forbidden example:

```python
api_key = os.getenv("API_KEY", "default-secret")
```

Required secrets must fail validation instead.

## 8. Secrets and Sensitive Configuration

### 8.1 Git Protection

Whenever `.env` is created or edited, verify that it is excluded by `.gitignore`.

Do not assume the exclusion exists without checking.

### 8.2 Secret Exclusion from Examples

Never place real values for the following in `.env.example`:

- passwords;
- API keys;
- access tokens;
- refresh tokens;
- bot tokens;
- private keys;
- signing secrets;
- credentials;
- other sensitive values.

Use placeholders instead.

### 8.3 Secret Exclusion from Logs

Never log:

- secret values;
- credentials;
- complete environment dumps containing sensitive variables;
- tokens;
- private keys;
- passwords.

Validation errors may identify the name of the invalid variable but must not print its secret value.

Good:

```text
TELEGRAM_BOT_TOKEN is missing or invalid
```

Forbidden:

```text
TELEGRAM_BOT_TOKEN is invalid: 123456:secret-value
```

### 8.4 Secret Handling with the User

When a new secret is required:

1. add the variable to the configuration structure;
2. add a safe placeholder to `.env.example`;
3. implement or update startup validation;
4. tell the user which variable must be filled locally;
5. do not ask the user to send the secret value in chat;
6. wait for the user to confirm that the value has been configured;
7. after confirmation, restart the affected services and perform the required checks.

Do not block unrelated implementation work solely because a secret value has not yet been filled if the remaining work can be completed safely without it.

## 9. Environment Isolation

Development and production may use the same `.env` filename when they run on separate machines.

Do not require separate files such as `.env.development` or `.env.production` unless the project explicitly needs them.

Each machine must maintain its own environment-specific `.env` values.

Do not mix production credentials, production databases, production customer data, or other production-sensitive resources into development or test environments unless the user explicitly requests and understands the risk.

Configuration examples must remain environment-neutral where practical.

## 10. Docker and Runtime Configuration

### 10.1 Runtime Injection

Environment-specific values and secrets must be supplied to containers at runtime rather than embedded into Docker images.

Prefer runtime mechanisms such as:

- `env_file`;
- Compose `environment`;
- platform-provided runtime environment variables;
- other project-approved runtime secret mechanisms.

Do not place production secrets in Dockerfiles or image layers.

### 10.2 Shared `.env`

A single `.env` file may be used by multiple related services or containers when appropriate.

Each service should consume only the variables it needs.

Do not create multiple `.env` files solely because several containers exist.

### 10.3 Docker Compose Service Naming

Use short role-based Docker Compose service names when possible.

Prefer:

```yaml
services:
  api:
  postgres:
  redis:
```

Avoid forcing repository-prefixed service keys unless the project has a concrete need for globally unique identifiers.

### 10.4 `container_name`

Do not set `container_name` by default.

Allow Docker Compose to manage project-scoped container names unless a fixed container name is required for a specific integration or operational reason.

This avoids unnecessary naming conflicts and allows multiple copies of the same project to run more easily.

### 10.5 Internal Service Discovery

Inside Docker Compose networks, prefer service names for connectivity.

Example:

```env
DATABASE_HOST=postgres
```

Do not depend on manually constructed container names when the Compose service name already provides stable internal DNS.

## 11. Naming Standards

### 11.1 Environment Variables

Environment variable names must use uppercase `SNAKE_CASE`.

Examples:

```text
APP_PORT
DATABASE_HOST
TELEGRAM_BOT_TOKEN
BACKUP_INTERVAL_HOURS
```

Names must describe the actual responsibility of the setting.

Avoid ambiguous names such as:

```text
TOKEN
HOST
TIMEOUT
```

when the project contains multiple possible meanings.

Prefer:

```text
TELEGRAM_BOT_TOKEN
DATABASE_HOST
REQUEST_TIMEOUT_SECONDS
```

### 11.2 Component Identifiers

Use repository or project prefixes for globally scoped identifiers when they are actually needed.

Use role-based names for local Docker Compose services.

Do not mechanically apply a repository prefix to every service, container, database, or logical component.

Choose naming based on the scope in which uniqueness is required.

## 12. Complex Configuration Values

For complex values such as:

- JSON;
- multiline text;
- certificates;
- PEM keys;
- connection strings;
- URLs with query parameters;
- strings containing parser-sensitive characters;

verify how the target parser handles quoting, escaping, interpolation, and multiline values.

Do not assume shell, dotenv, Docker Compose, Python, and Node.js parsers behave identically.

If a value becomes difficult or unsafe to represent directly in an environment variable, use a project-appropriate alternative such as a mounted file or runtime secret mechanism.

## 13. Configuration Verification

After changing environment configuration:

- verify that `.env` remains excluded by `.gitignore`;
- verify that `.env.example` is synchronized structurally;
- verify that no real secrets were copied into tracked files;
- validate Docker Compose configuration when applicable;
- start or restart affected services when configuration values required for startup are available;
- inspect startup logs;
- confirm that valid configuration starts successfully;
- confirm that required missing or invalid configuration fails fast;
- confirm that validation errors do not expose secret values;
- confirm that Docker images do not contain newly introduced environment-specific secrets.

When a required secret has not yet been filled by the user:

- complete all checks that do not require the secret;
- tell the user which variable must be filled locally;
- do not ask for the secret value;
- perform restart and runtime verification after the user confirms it has been configured.

Do not claim runtime verification was completed if the required secret was not available.

## 14. Definition of Done

A configuration task is complete only when all applicable conditions are satisfied:

- configuration variables are correctly named;
- `.env` is logically organized;
- `.env.example` reflects the current configuration structure;
- safe defaults and placeholders are correct;
- no real secrets are present in `.env.example` or other tracked configuration files;
- `.env` is excluded from Git;
- startup validation covers required types and constraints;
- required invalid configuration fails fast;
- secret values are not exposed through logs or validation errors;
- Docker configuration uses runtime injection for environment-specific values;
- Docker service naming follows the appropriate local or global scope;
- parser-sensitive values are handled correctly;
- affected services start successfully after required values are available;
- relevant startup logs have been inspected;
- configuration-related failure behavior has been tested.

If any applicable check cannot be completed, mark it explicitly as unverified rather than treating the configuration change as fully validated.
