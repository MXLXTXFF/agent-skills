---
name: Public Changelog and Devlog Management
description: >
  Governs cumulative public changelog and devlog preparation for end users,
  including release-note accumulation, user-facing filtering, safe security
  communication, automatic semantic version selection, pre-release handling,
  terminology consistency, publication verification, and copy-paste-ready output.
---

# Public Changelog and Devlog Management

## 1. Purpose

Use this skill to maintain and prepare public release communication for end users.

The skill manages two related but distinct outputs:

- `Changelog` — concise release notes containing only completed user-visible changes included in a specific release;
- `Devlog` — a broader user-facing development log that may also include meaningful internal improvements, operational improvements, and safely generalized security work when they are worth communicating.

Both outputs must remain understandable to end users and must avoid exposing unnecessary implementation details.

## 2. Activation

Apply this skill when:

- a user-visible change is completed and should be accumulated for a future release;
- a completed internal improvement is appropriate for the devlog;
- a release or version is being prepared;
- the user explicitly requests a changelog or devlog;
- a thematic set of completed changes must be summarized for publication.

Do not wait until release time to reconstruct all changes from chat memory.

Accumulate eligible completed changes during development so release context is preserved.

## 3. Cumulative Release Tracking

Use `CHANGELOG.md` as the persistent source of truth for accumulated release notes.

Maintain an `Unreleased` section throughout the development cycle so completed publishable changes survive chat restarts, model changes, and long development sessions.

Recommended structure:

```md
# Changelog

## Unreleased

### Changelog
- ...

### Devlog
- ...
```

Create only the subsections that currently contain entries.

When a completed task produces a publishable change:

1. determine whether it belongs in the changelog, devlog, or both;
2. record the concise public-facing result under `Unreleased`;
3. avoid duplicate entries;
4. update an existing pending entry when later work refines the same user-facing change;
5. keep planned or incomplete work out of the pending release notes.

The cumulative notes represent only completed and verified work.

Do not treat discussion, intention, RoadMap items, or partially implemented features as publishable release content.

### 3.1 Release Finalization

When a release is finalized:

1. determine or confirm the release version;
2. generate the copy-paste-ready changelog or devlog from verified `Unreleased` entries;
3. move the released entries from `Unreleased` into the corresponding version section in `CHANGELOG.md`;
4. preserve only unreleased remaining items under `Unreleased`;
5. do not duplicate the same item in both released and unreleased sections.

Example:

```md
## v1.4.0

### Changelog
- ...

### Devlog
- ...
```

Do not delete released history from `CHANGELOG.md` unless the user explicitly requests a different retention model.

## 4. Source Hierarchy

Build release notes from verified project evidence.

Preferred source order:

1. completed and verified changes from the active development cycle;
2. cumulative pending changelog/devlog entries;
3. `CHANGELOG.md`, when present and relevant;
4. Git history or release commits when the release is being reconstructed from repository state;
5. current chat context as supporting context.

Do not include a change solely because it was discussed in chat.

A change must be implemented, verified, and part of the target release.

## 5. Audience

Public changelog and devlog content is written for end users of the service.

Do not write release notes for maintainers, backend developers, database administrators, or infrastructure engineers unless the user explicitly requests a technical/internal publication.

## 6. Language

By default, write changelog and devlog publications in Russian.

Use another language only when the user explicitly requests it.

Do not automatically generate multiple localized versions merely because the product supports multiple languages.

If multiple languages are requested, preserve the same meaning and feature terminology across translations.

## 7. Style

Use:

- official tone;
- dry professional language;
- concise technical wording understandable to users;
- direct statements;
- real product terminology.

Avoid:

- slang;
- promotional language;
- hype;
- filler;
- exaggerated claims;
- unsupported superlatives;
- conversational introductions;
- subjective praise.

Bad:

```text
• Значительно улучшена невероятная стабильность сервиса.
```

Good:

```text
• Исправлена ошибка разрыва соединения после повторной авторизации.
```

## 8. Changelog vs Devlog

### 8.1 Changelog

The changelog contains only changes that directly affect the user experience in the released version.

Typical changelog content:

- new user-facing features;
- user-visible bug fixes;
- localization additions;
- meaningful UX improvements;
- measurable user-visible performance improvements;
- externally visible behavior changes.

Do not include internal-only engineering work.

### 8.2 Devlog

The devlog may contain:

- all suitable changelog items;
- notable internal reliability improvements;
- meaningful infrastructure improvements;
- maintainability work worth communicating at a high level;
- security improvements described safely;
- significant performance work even when the implementation itself is internal.

The devlog must still avoid implementation-sensitive details.

Translate internal work into its meaningful operational or user-facing outcome.

Example:

Bad:

```text
• Refactored refresh token service and changed Redis session structure.
```

Good:

```text
• Повышена стабильность долгих пользовательских сессий и повторной авторизации.
```

## 9. User-Facing Translation Rule

Never expose implementation detail when the same change can be described through its verified effect.

Bad:

```text
• Оптимизирован SQL-запрос списка серверов.
```

Good:

```text
• Ускорена загрузка списка серверов.
```

Bad:

```text
• Добавлен новый backend endpoint для подписки.
```

Good:

```text
• Добавлено отображение актуальной информации о подписке.
```

Only make such translations when the public outcome is real and verified.

Do not invent user impact for purely internal work.

## 10. Internal-Only Changes

Exclude internal-only changes from the changelog.

Examples:

- code refactoring;
- dependency cleanup;
- test reorganization;
- CI changes;
- internal logging cleanup;
- database migration mechanics;
- internal API restructuring;
- architecture cleanup;
- Docker build changes with no meaningful user or operational impact.

Such changes may appear in the devlog only when they represent a meaningful reliability, security, maintainability, or operational improvement worth communicating.

## 11. Security Communication

### 11.1 Devlog Security Category

Security-related public communication belongs in the devlog under a dedicated category:

```text
Безопасность
```

Do not add a separate Security category to the normal changelog unless the user explicitly requests it.

### 11.2 Safe Disclosure

Do not disclose exploit-enabling technical details.

Avoid publishing:

- vulnerable endpoint paths when unnecessary;
- exact exploit steps;
- payloads;
- authorization bypass mechanics;
- internal security architecture;
- vulnerable dependency details when disclosure creates avoidable risk;
- secret locations;
- configuration weaknesses that enable exploitation.

Bad:

```text
• Исправлен IDOR в GET /api/users/{id}, позволявший читать чужие данные.
```

Good:

```text
• Усилена проверка доступа к пользовательским данным.
```

When a security change should not be publicized safely, omit it from the public devlog.

## 12. Supported Public Categories

Use only categories that contain actual items.

For changelog:

- `Новое`;
- `Исправления`;
- `Улучшения`.

For devlog:

- `Новое`;
- `Исправления`;
- `Улучшения`;
- `Безопасность`.

Do not render empty categories.

## 13. Grouping Threshold

When the publication contains five or fewer items, a flat bullet list is acceptable.

When the publication contains more than five items, group entries into the applicable categories.

Do not force grouping when it reduces readability.

## 14. Item Priority

Order items by user significance, not by commit order.

Preferred priority:

1. new user-facing features;
2. important user-visible fixes;
3. meaningful UX or performance improvements;
4. localization changes;
5. minor fixes.

Within each category, place the most important user-facing changes first.

Security items in devlog should be ordered by practical significance without revealing severity details that would create unnecessary risk.

## 15. Bullet Format

Use the `•` bullet character for publication items.

Each bullet should normally contain one concise sentence describing one meaningful change.

Example:

```text
• Добавлена возможность выбора протокола подключения в настройках профиля.
```

Do not create nested implementation lists in the public publication.

## 16. Combining Related Minor Changes

Combine small closely related fixes when separate entries add no useful information.

Example:

Instead of:

```text
• Исправлена кнопка сохранения в настройках.
• Исправлено выравнивание кнопки отмены.
• Исправлена подсказка рядом с переключателем.
```

Prefer:

```text
• Исправлены ошибки отображения и работы элементов управления в настройках.
```

Do not combine separate important features or unrelated changes merely to reduce line count.

## 17. Separate Significant Features

Each significant user-facing feature should normally have its own bullet.

Bad:

```text
• Улучшены авторизация, подписки, серверы, интерфейс и локализация.
```

Good:

```text
• Добавлено отображение текущего статуса подписки.
• Улучшена повторная авторизация после истечения сессии.
• Расширена локализация интерфейса.
```

## 18. Terminology Consistency

Use the real product terminology from:

- user interface;
- README;
- existing release notes;
- established public naming.

Do not invent new public names for existing features.

Do not alternate between multiple terms for the same feature unless the product itself does.

## 19. Versioning

Use Semantic Versioning-style release selection when the user has not provided a version.

### 19.1 Patch

Increment the third number:

```text
x.y.Z
```

Use for:

- bug fixes;
- small user-visible corrections;
- non-breaking maintenance releases.

Example:

```text
1.4.2 → 1.4.3
```

### 19.2 Minor

Increment the second number and reset patch to zero:

```text
x.Y.0
```

Use for:

- new backward-compatible user features;
- new localization;
- meaningful UX improvements;
- substantial backward-compatible enhancements.

Example:

```text
1.4.3 → 1.5.0
```

### 19.3 Major

Increment the first number and reset the others:

```text
X.0.0
```

Use primarily for:

- breaking changes;
- major redesign;
- substantial changes to user flows;
- compatibility-breaking behavior;
- fundamental public product changes.

Do not choose a major version merely because many files changed internally.

## 20. Automatic Version Selection

If the user does not specify a release version, determine the appropriate next version automatically from:

- the current project version;
- the completed release scope;
- compatibility impact;
- the versioning rules in this skill.

If a release version is explicitly provided by the user or already established by the project, use that version.

Do not override an explicitly established release version without user instruction.

## 21. Pre-Release Versions

Support pre-1.0 development versions.

Examples:

```text
0.3.0
0.4.0
0.4.1
```

Do not force an MVP or actively evolving project to `1.0.0` merely because a large update occurred.

Use `1.0.0` when the user or project explicitly establishes the first stable public release.

For `0.x.y` projects, continue using patch/minor increments consistently while recognizing that the product is still pre-stable.

## 22. Release Header

Use a header containing the service name and release version.

Example:

```text
VPN Service v1.4.0
```

Do not include the date in the header by default.

## 23. Verification Before Publication

Before producing the final publication:

- verify the target release version;
- verify that each included change is completed;
- verify that each included change belongs to the target release;
- exclude planned or in-progress work;
- exclude internal-only changes from changelog;
- convert suitable internal changes into safe devlog outcomes;
- check for duplicate entries;
- check category correctness;
- check product terminology;
- check that performance claims are supported;
- check that security wording does not expose exploit details;
- check that no secrets or credentials are present;
- check that internal endpoint names, schema details, database details, or architecture details are not unnecessarily exposed;
- check that all claims are supported by verified project state.

If a change cannot be verified, do not present it as confirmed release content.

## 24. Performance Claims

Only publish specific performance claims when they are backed by real measurements.

Allowed:

```text
• Время загрузки списка серверов сокращено с 1,2 с до 0,5 с.
```

when verified.

If exact metrics are not available, use a non-quantified but still verified statement only when the improvement is observable and confirmed:

```text
• Ускорена загрузка списка серверов.
```

Do not claim substantial performance improvement based only on implementation assumptions.

## 25. Output Format

The final changelog or devlog must be copy-paste ready.

Do not include explanatory assistant text inside the publication block.

Example:

```text
VPN Service v1.4.0

Новое

• Добавлена возможность выбора протокола подключения.
• Расширена локализация интерфейса.

Исправления

• Исправлена ошибка повторной авторизации после истечения сессии.

Улучшения

• Ускорена загрузка списка серверов.
```

For devlog, include the `Безопасность` section only when applicable.

## 26. Publication Readiness

A generated publication must be suitable for direct use in:

- Telegram;
- Discord;
- website release notes;
- public service announcement;
- release post.

Do not require the user to remove technical metadata or assistant commentary before publication.

## 27. Definition of Done

A changelog or devlog publication is complete only when all applicable conditions are satisfied:

- the correct publication type was selected;
- cumulative release notes were considered;
- only completed and verified work was included;
- planned and in-progress work was excluded;
- changelog contains only user-visible changes;
- devlog contains only meaningful public-facing or operationally relevant changes;
- internal implementation details were removed;
- internal work was translated into verified user or operational outcomes when appropriate;
- security changes were described safely;
- the `Безопасность` category appears only in devlog and only when needed;
- no exploit-enabling security details are present;
- no secrets or credentials are present;
- product terminology is consistent;
- significant features are separated into distinct bullets;
- minor related fixes are combined when useful;
- items are ordered by user significance;
- empty categories are omitted;
- grouping is used only when it improves readability;
- the publication is written in Russian by default;
- other languages are generated only when explicitly requested;
- the release version was preserved when already established;
- otherwise the next version was selected according to the versioning rules;
- pre-release versioning is preserved for pre-1.0 projects;
- the header contains service name and version without a date by default;
- performance claims are supported by real verification;
- the publication is copy-paste ready;
- every factual claim is supported by verified release state.

If any included change cannot be verified, exclude it or clearly resolve its status before publication.
