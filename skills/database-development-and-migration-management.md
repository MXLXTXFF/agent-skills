---
name: Database Development and Migration Management
description: >
  Governs database schema design, isolated test databases, migrations, backup and
  restore workflows, cross-project naming conventions, data safety, indexing,
  fixtures, schema drift detection, verification, and documentation synchronization.
---

# Database Development and Migration Management

## 1. Purpose

Use this skill whenever a task creates, changes, migrates, optimizes, verifies, backs up, restores, or documents a database or persistent schema.

The goal is to keep database work reproducible, isolated, recoverable, consistent across projects, and safe for existing data.

This skill governs:

- test database isolation;
- schema design;
- cross-project database conventions;
- migrations;
- data migrations;
- backup and restore workflows;
- destructive changes;
- indexes and constraints;
- fixtures and seed data;
- schema drift detection;
- database compatibility;
- post-migration verification;
- documentation synchronization.

## 2. Test Database Isolation

Automated and integration tests must never use the active development, staging, or production database for temporary test data.

Use one of the following:

- a dedicated test database;
- a temporary database clone;
- an ephemeral database container;
- another isolated database instance created specifically for tests.

The test database must use, as closely as practical:

- the same database engine;
- a compatible database version;
- the same required extensions;
- the same migration history;
- the same schema semantics.

Do not substitute a materially different database engine merely for testing convenience when production behavior depends on engine-specific semantics.

Example to avoid:

```text
Production: PostgreSQL
Tests: SQLite
```

when the application relies on PostgreSQL-specific behavior.

## 3. Test Data Must Not Pollute Main Databases

Do not use the main development database for test inserts merely because the test cleans the rows afterward.

Temporary test writes may still affect:

- sequences;
- identity counters;
- triggers;
- audit tables;
- outbox/event tables;
- timestamps;
- statistics;
- background workers;
- related records;
- caches;
- external integrations.

Prefer:

```text
temporary test database
→ apply migrations
→ load fixtures
→ run tests
→ destroy/reset test database
```

or a persistent dedicated test database that is reset only within the test environment.

## 4. Backup Approval Before Main Database Changes

Because schema and data changes are first developed and verified against an isolated test database, do not automatically create a dump of the main database before every development iteration.

Before applying an approved schema or bulk-data change to the active development, staging, or production database:

1. explain that the main database is about to be changed;
2. ask the user whether to create an out-of-schedule backup/dump first;
3. do not silently create or skip the backup decision;
4. if the user approves, complete and verify the backup before applying the database change.

This explicit backup decision applies regardless of environment when the target database contains state worth preserving.

## 5. Existing Backup Delivery Workflow

If the project already has a configured backup workflow, reuse it for the out-of-schedule backup.

Examples:

- backup uploaded to S3-compatible storage;
- backup sent through an established Telegram backup process;
- backup sent to another configured remote backup destination;
- backup produced by an existing project script or scheduled job.

Do not invent a parallel backup mechanism when the project already has a trusted, tested one.

Trigger the same normal backup process as an unscheduled backup when practical.

Verify that the backup completed successfully.

## 6. Backup Scope

A backup request is relevant before operations such as:

- applying a schema migration to the main database;
- `ALTER TABLE`;
- table creation or removal;
- column type conversion;
- destructive constraint changes;
- bulk data migration;
- large `UPDATE`;
- large `DELETE`;
- cleanup scripts;
- imports that transform existing data;
- destructive resets;
- major index or schema restructuring.

Do not create a dump before every normal application transaction such as:

- user registration;
- payment creation;
- subscription update;
- ordinary CRUD performed by the running application.

## 7. Backup Compression

When a backup is created, use lossless compression at the maximum practical compression level supported by the chosen trusted tool.

Prefer:

```text
native compressed database dump
```

or:

```text
native dump
→ lossless maximum-compression archive
```

Choose the format according to the database engine and existing project backup workflow.

Do not trade recoverability for a smaller archive.

## 8. Backup Naming

Use:

```text
<project>-<database>-<timestamp>.<format>
```

Examples:

```text
vpn-backend-postgres-20260818T064200.dump.zst
billing-postgres-20260818T070500.sql.gz
```

The environment name does not need to be present in the filename when the server or backup destination already unambiguously identifies the environment.

Use a filename-safe timestamp format without colon characters.

## 9. Backup Verification

A backup is not considered successful merely because a file was created.

Verify applicable:

- file exists;
- file size is plausible;
- archive integrity passes;
- dump is readable by the database tooling;
- configured remote upload completed;
- expected destination contains the artifact.

For major, destructive, or production-sensitive migrations, verify restore into a temporary database when practical.

If restore verification cannot be performed, report it explicitly as unverified.

## 10. Backup Security

Database backups are sensitive artifacts.

Do not commit or push backups to Git or GitHub.

Exclude applicable backup artifacts and backup directories from Git tracking.

Examples:

```gitignore
backups/
*.dump
*.dump.gz
*.dump.zst
*.sql.gz
*.sql.zst
```

Protect backup files with appropriate filesystem permissions.

For production backups, consider encryption when:

- the backup is transferred;
- the backup is stored remotely;
- the backup is retained long term;
- the dump contains sensitive user or credential-related data.

Do not place database passwords, connection strings, or secrets in backup filenames.

## 11. Backup Retention and Deletion

Do not automatically delete backup artifacts after a successful migration.

Backups are deleted only:

- after an explicit user request in chat;
- manually by the user;
- according to an already established project retention policy that the user has approved.

Do not invent or change backup retention policy silently.

## 12. Backup Order

When a backup is approved, use this order:

```text
current database
→ create backup
→ verify backup
→ apply migration/change
→ verify database
```

Never postpone the backup until after a risky change has already been applied.

## 13. Migration-Only Persistent Schema Changes

Every persistent schema change must be represented by the project's migration mechanism.

Do not leave a persistent schema change as a manual one-off SQL command executed on a server.

Correct workflow:

```text
migration file
→ review
→ test database
→ verification
→ optional approved backup of main DB
→ apply to target DB
→ verify
```

The complete current schema must be reproducible from the migration history.

## 14. Migration Naming

Use sequential, zero-padded three-digit migration numbers followed by an English `snake_case` migration name.

Format:

```text
000_migration_name
```

Examples:

```text
001_create_users
002_create_subscriptions
014_add_subscription_status
105_add_payment_external_id
```

Migration names must be concise and describe the actual schema or data change.

Do not use vague names such as:

```text
014_update_db
015_fix_stuff
```

Use the project's framework-required filename extension or wrapper around this naming convention.

## 15. Migration Sequence

Migration numbers must be sequential within the project migration history.

Before creating a migration:

1. inspect the existing migration sequence;
2. determine the next available number;
3. avoid reusing an existing number;
4. avoid creating conflicting parallel migration numbers unless the migration framework explicitly supports branching and the project has adopted it.

## 16. Applied Migrations Are Immutable

Do not edit an already applied migration to change historical behavior.

If:

```text
001_create_users
002_create_subscriptions
```

have already been applied and the users schema needs correction, create a new migration such as:

```text
003_fix_users_schema
```

Do not rewrite migration history after it has been used by another database.

An unapplied local migration may be adjusted before it becomes part of shared or persistent migration history, but avoid unnecessary history rewriting.

## 17. Fresh and Upgrade Migration Paths

Verify both applicable paths.

### 17.1 Fresh Database

```text
empty database
→ all migrations
→ current schema
```

This verifies that a new environment can be created from scratch.

### 17.2 Existing Database

```text
previous schema
→ new migration
→ current schema
```

This verifies that an already-running project can upgrade correctly.

Do not rely only on fresh-database testing.

## 18. Safe Migration Strategy

For potentially breaking schema changes, prefer staged migrations.

Typical strategy:

```text
expand
→ migrate data
→ switch application
→ verify
→ contract
```

Example for renaming a populated field:

```text
1. add new column
2. backfill data
3. update application to use new column
4. verify
5. remove old column later
```

For pre-release MVP environments, broader schema restructuring is acceptable when technically justified and approved, but data safety and verification rules still apply.

## 19. Destructive Changes

Treat the following as destructive or potentially destructive:

- `DROP TABLE`;
- `DROP COLUMN`;
- `TRUNCATE`;
- large `DELETE`;
- type narrowing;
- incompatible type conversion;
- constraints that may reject existing data;
- destructive reset;
- data rewrite that cannot be trivially reversed.

Before applying a destructive change, determine:

- whether data exists;
- whether the data matters;
- whether data loss is acceptable;
- whether a backup was offered and, if approved, verified;
- whether restore is possible;
- whether the user explicitly approved the destructive impact when required.

Do not perform production-relevant destructive data loss without explicit approval.

## 20. Transactional Migrations

Use transactional migrations when the database engine and migration operation support them safely.

Conceptually:

```text
BEGIN
→ migration
→ validation
→ COMMIT
```

On failure:

```text
ROLLBACK
```

Do not assume every database engine or every DDL operation is fully transactional.

Verify version-specific behavior using official database documentation when needed.

## 21. Cross-Project Database Design Standard

Use consistent database conventions across projects when different projects represent the same concept.

The same semantic value should use the same:

- naming;
- type;
- unit;
- status vocabulary;
- timestamp semantics;
- identifier convention;

unless a project-specific requirement justifies a difference.

Do not create unnecessary variants such as:

```text
Project A: created_at
Project B: creation_time
Project C: created
```

when all three fields represent the same concept.

Consistency applies to meaning, not merely appearance.

Do not force unrelated domain concepts into the same schema vocabulary when their semantics differ.

## 22. Table Naming

Default to:

```text
snake_case
plural table names
```

Examples:

```text
users
subscriptions
vpn_servers
payment_transactions
```

Follow an already established clear project convention if changing it would create unnecessary incompatibility.

## 23. Primary and Foreign Key Naming

Default primary key:

```text
id
```

Default foreign key pattern:

```text
<entity>_id
```

Examples:

```text
user_id
subscription_id
server_id
```

Use consistent naming across projects for equivalent relationships.

## 24. Timestamp Columns

Use standard timestamp names:

```text
created_at
updated_at
deleted_at
```

when the semantics apply.

Store canonical timezone-aware timestamps.

Prefer UTC for persisted application timestamps while preserving timezone-aware semantics.

For PostgreSQL, use timezone-aware types such as `timestamptz` when appropriate.

Do not store ambiguous local server time without timezone semantics.

API serialization remains governed by the API Development skill and uses explicit RFC 3339 / ISO 8601 timestamps.

## 25. Boolean Naming

Prefer names that express boolean meaning clearly.

Examples:

```text
is_active
is_verified
is_enabled
has_access
```

Avoid inconsistent patterns for equivalent concepts such as:

```text
active_flag
enabled_status
userVerified
```

when no compatibility requirement exists.

## 26. Status Vocabulary

When multiple projects represent the same domain state, use the same stable status vocabulary where semantics are genuinely identical.

Example:

```text
pending
active
disabled
expired
cancelled
```

Do not use:

```text
active / disabled
enabled / inactive
1 / 0
```

for the same semantic state across different projects without a reason.

Do not force unrelated entities to share the same status values merely for consistency.

## 27. Money

Store monetary amounts using exact decimal types.

Use:

```text
amount → DECIMAL / NUMERIC
currency → ISO currency code
```

Do not store monetary values in binary floating-point types.

Keep database semantics consistent with the API contract, where money is represented as a decimal string plus currency.

## 28. Nullability

Choose nullability intentionally.

Use:

```text
required value → NOT NULL
genuinely absent/unknown value → nullable
```

Define one clear representation for absence.

Do not mix multiple absence conventions such as:

```text
NULL
''
0
'unknown'
```

for the same semantic meaning.

## 29. Constraints

Use database constraints for invariants that the database can reliably enforce.

Consider:

- `NOT NULL`;
- `UNIQUE`;
- foreign keys;
- valid ranges;
- non-negative amounts;
- stable relationship constraints.

Application validation does not replace all database integrity guarantees, especially under concurrency.

Do not move excessive business workflow logic into complex database triggers without a strong reason.

## 30. Foreign Key Behavior

Use explicit foreign keys when they match the data model.

Choose delete behavior intentionally:

```text
ON DELETE RESTRICT
ON DELETE CASCADE
ON DELETE SET NULL
```

Do not use cascading deletion by default.

Evaluate data retention and business impact before allowing related historical data to disappear automatically.

## 31. Index Policy

Create indexes based on real query patterns.

Use:

```text
important query
→ frequency
→ selectivity
→ execution plan
→ index candidate
→ benchmark
→ verify again
```

Do not create an index merely because a column appears in:

- `WHERE`;
- `JOIN`;
- `ORDER BY`.

Consider:

- read benefit;
- write cost;
- index size;
- selectivity;
- workload frequency.

## 32. Unique Constraints and Concurrency

When a business invariant truly requires uniqueness, consider a database-level unique constraint.

Examples:

- unique external payment ID;
- unique webhook event ID;
- one unique resource per domain key.

Do not rely solely on an application-level pre-check when concurrent requests can violate the invariant.

## 33. Test Fixtures

Use controlled fixtures in the test database.

Examples:

- known users;
- known subscriptions;
- known payment states;
- known expiration states.

Tests must not depend on random stale data from development databases.

After tests, the test database may be:

- rolled back;
- reset;
- recreated;
- destroyed.

Only the isolated test database may be treated this way automatically.

## 34. Seed Data vs Test Data

Keep seed data and test fixtures separate.

### Seed Data

Data required for the application to operate.

Examples:

- system roles;
- reference data;
- default static configuration;
- initial plans when they are true application data.

### Test Fixtures

Data created only to test behavior.

Examples:

- test users;
- expired test subscriptions;
- failed test payments;
- simulated edge states.

Do not mix test-only records into application seed data.

## 35. Production Data in Test Environments

Do not copy raw production data into test environments without a justified need.

When production-like data is required:

```text
production dump
→ sanitize / anonymize
→ isolated test database
```

Remove or transform sensitive information such as:

- passwords or password-related artifacts;
- access tokens;
- refresh tokens;
- personal data;
- payment information;
- VPN credentials;
- private configuration;
- secrets.

Use the minimum production-derived data necessary for the test purpose.

## 36. Schema Drift Detection

Check that:

```text
migration history
↔ actual database schema
```

remain consistent.

Do not assume the database is correct merely because the migration tracking table reports all migrations as applied.

If manual schema drift is found:

1. identify the difference;
2. determine how it occurred;
3. represent the desired persistent state in migrations;
4. avoid silently normalizing production data or schema without approval.

## 37. Database Version and Extension Compatibility

Verify applicable:

- database engine;
- database version;
- extensions;
- migration support;
- development/test/production compatibility.

Do not assume that an extension or feature available in development also exists in production.

Use official documentation for version-sensitive database behavior.

## 38. Database Connections and Privileges

Runtime application database users should have only the privileges required for normal application operation.

Do not use a database superuser for the application runtime without a justified need.

Separate identities such as:

```text
migration user
application user
backup user
```

may be used when the project benefits from that separation.

Do not introduce unnecessary credential and role complexity into a small MVP without a practical security or operational benefit.

## 39. Database Secrets

Database credentials and connection strings are configuration secrets.

Do not place them in:

- source code;
- migration files;
- backup filenames;
- logs;
- reports;
- test fixtures.

Use the project's environment/configuration management process.

## 40. Backup Metadata

When useful, record enough metadata to identify a backup.

Relevant fields may include:

- project;
- database;
- database engine/version;
- timestamp;
- current migration version;
- Git commit;
- dump format;
- compression format;
- verification result;
- remote destination when applicable.

Use an existing backup system's metadata if it already records these facts.

Do not create redundant metadata files without practical value.

## 41. Restore Strategy

Before a major, destructive, or high-risk migration, understand the recovery path.

Possible strategies:

```text
transaction rollback
```

or:

```text
stop application
→ recreate/restore database
→ verify restored state
→ restart application
```

A backup without a feasible restore strategy is incomplete protection.

For important migrations, document or verify the restore path before applying the change to the main database.

## 42. Post-Migration Verification

After applying a database change, verify applicable:

- migration completed;
- expected migration version is recorded;
- schema matches the intended design;
- constraints are correct;
- indexes are present when expected;
- data was preserved;
- transformed data is correct;
- application starts;
- targeted tests pass;
- integration tests pass where applicable;
- logs contain no new database errors;
- important queries work.

For data migrations, compare relevant metrics such as:

- row count before/after;
- number of transformed rows;
- unexpected `NULL` values;
- invalid records;
- duplicates;
- failed transformations.

Use only comparisons that are meaningful for the migration.

## 43. Manual SQL

Read-only diagnostic SQL is allowed when needed.

Temporary data manipulation may be allowed in a disposable test database.

Do not leave a persistent schema fix only as manually executed SQL.

Any persistent schema change must be represented by a migration.

## 44. Database Change Plan

A separate database change plan is not required for every small additive migration when the user has already explicitly requested the feature.

For material changes such as:

- table redesign;
- large data migration;
- type conversion;
- destructive cleanup;
- schema split;
- schema merge;
- significant relationship redesign;

prepare a plan first.

The plan should include:

```text
current schema
proposed schema
migration strategy
data preservation
compatibility impact
downtime impact
backup decision
restore strategy
verification
```

Obtain approval before implementing material redesigns.

## 45. Documentation Synchronization

After a meaningful database change, check applicable documentation.

### README

Update when changing:

- database setup;
- migration commands;
- required extensions;
- backup/restore procedure;
- development/test database workflow.

### AGENT

Update when changing:

- database architecture;
- important schema invariants;
- project-specific database relationships;
- important persistent-state assumptions.

### API Development

Apply the API Development skill when the database change affects a public or inter-service contract.

### Cross-Session Synchronization

Update the relevant `*_SYNC.md` when another service must adapt.

Do not duplicate detailed schema documentation unnecessarily when the migration/schema itself is the source of truth.

## 46. User-Facing Handoff

After database work, report the database state concisely.

Example:

```text
Database:
- Test database migration passed
- Migration 014_add_subscription_status created
- Main database backup was requested and approved
- Backup created and verified
- Migration applied successfully
- Application startup verified
```

If the user did not approve a backup:

```text
Database backup:
- Not created — user did not approve the out-of-schedule backup
```

If backup or restore verification could not be completed:

```text
Backup verification: unverified
```

Do not claim a backup or migration is verified when the relevant check was not performed.

## 47. Definition of Done

Database work is complete only when all applicable conditions are satisfied:

- tests did not use active development, staging, or production databases;
- a dedicated or ephemeral test database was used;
- the test database uses compatible database semantics;
- test data did not pollute the main database;
- schema changes were developed and tested against the isolated test database;
- before changing the main database, the user was explicitly asked whether to create an out-of-schedule backup;
- when backup was approved, the existing trusted backup workflow was reused where available;
- backup was created before the main database change;
- backup used maximum practical lossless compression;
- backup filename follows `<project>-<database>-<timestamp>.<format>`;
- backup integrity was verified;
- remote backup delivery was verified where applicable;
- restore verification was performed for high-risk changes when practical;
- backup artifacts are excluded from Git;
- backup artifacts were not automatically deleted;
- persistent schema changes are represented by migrations;
- migration naming follows `000_migration_name`;
- migration numbers are sequential and non-conflicting;
- already applied migrations were not rewritten;
- fresh-database migration path was tested;
- existing-database upgrade path was tested;
- destructive impact was evaluated;
- destructive data loss was not performed without required approval;
- transactional migration behavior was used when supported and appropriate;
- cross-project database naming conventions were followed where semantics match;
- table, key, timestamp, boolean, and status naming are consistent;
- timestamps are timezone-aware;
- monetary values use exact decimal storage;
- nullability is intentional;
- database constraints were reviewed;
- foreign-key delete behavior is intentional;
- indexes are justified by real query patterns;
- uniqueness invariants are enforced at the database level where appropriate;
- test fixtures are isolated from seed data;
- raw production data was not copied to test without sanitization;
- schema drift was checked where relevant;
- database version and extension compatibility were checked;
- runtime database privileges are appropriate;
- database secrets remain outside source, migrations, dumps, logs, and reports;
- backup metadata is sufficient to identify important restore points;
- restore strategy is understood for material changes;
- post-migration schema and data verification passed;
- application startup was verified;
- relevant tests passed;
- logs were checked;
- README was updated when required;
- AGENT was checked and updated when required;
- API impact was checked;
- cross-session synchronization impact was checked;
- the user received a concise database handoff.

If an applicable verification cannot be completed, mark it explicitly as unverified rather than claiming the database change is fully validated.
