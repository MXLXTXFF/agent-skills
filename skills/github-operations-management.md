---
name: GitHub Operations Management
description: >
  Governs safe Git and GitHub operations, including repository initialization,
  identity configuration, SSH remotes, GitHub CLI usage, commits, staging,
  branch policy, pushes, pull requests, GitHub Actions, repository settings,
  secrets protection, and remote synchronization.
---

# GitHub Operations Management

## 1. Purpose

Use this skill as the primary standard for all Git and GitHub operations performed during project development.

The goal is to preserve repository integrity, prevent accidental data loss, keep commits focused and reproducible, protect secrets, and ensure that remote GitHub operations occur only with the required user approval.

## 2. Activation

Apply this skill when a task involves one or more of the following:

- Git repository initialization;
- Git identity configuration;
- Git status, diff, staging, or commits;
- `.gitignore`;
- local or remote branches;
- GitHub remotes;
- fetch, pull, push, or history synchronization;
- GitHub CLI (`gh`);
- GitHub authentication;
- Pull Requests;
- GitHub Actions or CI checks;
- repository settings;
- repository visibility;
- collaborators or access control;
- tags or releases;
- secret exposure in repository history;
- repository cleanup related to tracked or ignored files.

## 3. Git Identity

### 3.1 Repository-Local Identity by Default

Before creating the first commit in a repository, verify that `user.name` and `user.email` are configured for that repository.

Prefer repository-local configuration:

```bash
git config user.name "..."
git config user.email "..."
```

Do not change global Git identity with `--global` unless the user explicitly requests a global configuration change.

### 3.2 Unknown Identity

If the required name or email cannot be determined from the repository, environment, or previously established user instructions, ask the user.

Never invent a Git author name or email.

## 4. Repository and Remote Discovery

### 4.1 Existing Repository

Before asking the user for a GitHub repository URL:

1. determine whether the current project is already a Git repository;
2. inspect configured remotes;
3. reuse the existing repository and remote when they are valid.

Typical checks:

```bash
git status
git remote -v
```

Do not ask for a repository URL when the required remote is already configured.

### 4.2 Missing Remote

Ask for the GitHub repository only when:

- no suitable remote exists;
- the repository must be connected to GitHub;
- a GitHub-specific operation is requested but the target repository cannot be determined.

Local Git work must not depend on having a GitHub remote.

## 5. Remote Transport

Use SSH for GitHub Git transport.

Prefer remotes in the form:

```text
git@github.com:OWNER/REPOSITORY.git
```

If a GitHub HTTPS remote is provided and SSH access is expected for the project, convert or configure it to use SSH before remote Git operations.

Do not request GitHub username/password authentication for Git transport.

## 6. GitHub CLI

### 6.1 Availability and Authentication

For GitHub-specific operations, check whether GitHub CLI is installed and authenticated.

When `gh` is available and authenticated, prefer it for GitHub-specific operations such as:

- repository inspection;
- Pull Requests;
- GitHub Actions and CI status;
- Issues;
- Releases;
- GitHub API operations supported by `gh`.

Use regular `git` for normal repository operations such as:

- status;
- diff;
- add;
- commit;
- branch inspection;
- fetch;
- push.

### 6.2 Missing or Unauthenticated `gh`

If `gh` is unavailable or not authenticated:

- inform the user;
- offer installation or authentication when appropriate;
- provide an alternative using regular Git when the task can be completed without `gh`;
- provide a manual GitHub workflow when no suitable command-line alternative exists.

Do not block Git-only work solely because `gh` is unavailable.

### 6.3 Authentication Safety

Do not ask the user to send GitHub tokens or credentials in chat.

Do not print, expose, or log authentication tokens.

Use authenticated local tooling or user-completed authentication flows.

## 7. Branch Policy

### 7.1 Default Branch

Use `main` as the only default working branch.

Do not create, switch to, or use `master` as the default branch.

Do not introduce alternative default branch names.

### 7.2 Additional Branches

Do not create or use additional branches such as:

```text
dev
develop
feature/*
release/*
hotfix/*
```

unless the user explicitly requests or establishes that branch workflow.

If the user explicitly defines another branch for a task, follow that instruction for the applicable scope.

### 7.3 Existing Non-Main Repository

If the repository currently uses `master` or another branch instead of `main`, do not silently rewrite remote branch structure.

Report the mismatch and obtain user approval before performing any branch migration, rename, default-branch change, or remote cleanup.

### 7.4 Branch Check

Before committing or performing remote operations, verify the current branch.

Example:

```bash
git branch --show-current
```

Do not silently change the project's branch strategy.

## 8. Protect Existing User Changes

### 8.1 Inspect Before Modification

Before staging or committing, inspect the current working tree.

Use appropriate checks such as:

```bash
git status
git diff
```

Identify pre-existing changes that were not created as part of the current task.

### 8.2 Manual User Changes

If a file contains manual user changes that are unrelated to the current task:

1. inspect what changed;
2. report the existence and nature of those changes to the user;
3. ask whether that file or those changes should be included in the current commit;
4. do not include them unless the user approves.

If the user declines, leave those changes only in the working tree.

### 8.3 Same-File Mixed Changes

If the user and the agent changed different parts of the same file:

- distinguish task-related hunks from pre-existing user hunks when practical;
- stage only task-related hunks unless the user approves including the manual changes;
- do not treat the entire file as agent-owned merely because the current task also modified it.

### 8.4 No Destructive Cleanup

Do not use destructive commands against uncommitted work without explicit user approval.

This includes, when they could discard user work:

```bash
git reset --hard
git clean -fd
git restore <file>
git checkout -- <file>
```

Do not automatically stash changes in a way that hides or risks losing user work.

## 9. Commit Scope

### 9.1 Task-Focused Commits

A commit must contain only changes that belong to the current task or changes the user explicitly approved for inclusion.

Avoid broad staging commands such as:

```bash
git add .
git add -A
```

when unrelated modifications are present.

Prefer explicit file or hunk staging.

### 9.2 Working State Requirement

Create a commit only when the current task changes are in a coherent and verified state.

Do not commit a deliberately broken intermediate state such as:

- failing imports introduced by the current task;
- syntax errors;
- startup failures caused by incomplete work;
- knowingly broken tests related to the current change;
- half-completed refactors that leave the changed path unusable.

The entire product roadmap does not need to be complete. Only the committed task scope must be internally consistent and appropriately verified.

## 10. Pre-Commit Verification

Before every commit:

1. inspect repository status;
2. review the unstaged diff;
3. verify `.gitignore`;
4. check for secrets and sensitive files;
5. identify unrelated user changes;
6. stage only approved task-related changes;
7. review the staged diff;
8. verify that the staged state matches the intended commit.

Use:

```bash
git diff --staged
```

or an equivalent staged-diff inspection before committing.

Do not create a commit without reviewing what is actually staged.

## 11. Conventional Commits

All commit messages must use Conventional Commits.

Use applicable types such as:

```text
feat:
fix:
refactor:
perf:
docs:
test:
build:
ci:
chore:
```

Optional scopes may be used when they add useful context:

```text
feat(auth): add refresh token rotation
fix(database): prevent duplicate subscriptions
docs(readme): update startup instructions
```

Commit messages must:

- be written in English;
- use concise technical language;
- describe the actual change;
- avoid slang;
- avoid unnecessary punctuation;
- avoid misleading or overly broad descriptions.

Do not force a scope when it adds no useful information.

## 12. `.gitignore` Management

### 12.1 Check Before Commit

Before every commit, verify that `.gitignore` remains appropriate for the project.

Check for project-generated or sensitive content such as:

- `.env`;
- logs;
- caches;
- temporary files;
- build artifacts when they should not be tracked;
- local editor files;
- database dumps;
- backups;
- private keys;
- local credentials;
- other project-specific generated files.

### 12.2 Narrow Ignore Patterns

Use the narrowest correct ignore rule.

Do not add overly broad patterns that could hide legitimate project files.

For example, do not ignore all `*.json` files when only one generated JSON file should be excluded.

### 12.3 Already Tracked Files

Do not assume adding a path to `.gitignore` removes an already tracked file from Git.

When sensitive or generated content is already tracked, handle the tracked state explicitly and safely.

## 13. Secret Protection

### 13.1 Never Commit Secrets

Do not commit or push:

- `.env` files containing real secrets;
- passwords;
- API keys;
- access tokens;
- refresh tokens;
- bot tokens;
- private keys;
- signing keys;
- database credentials;
- other sensitive credentials.

This rule applies even to private repositories.

### 13.2 Secret Found Before Commit

If a secret is found before commit:

- remove it from staging;
- prevent it from entering the commit;
- correct the source configuration;
- verify the staged diff again.

### 13.3 Secret Found in Local Unpushed History

If a secret entered a local commit but has not been pushed:

- do not push;
- inform the user;
- safely remove the secret from the local history;
- verify that the sensitive value is no longer present before any remote publication.

### 13.4 Secret Found After Push

If a secret has been pushed to GitHub, including a private repository, treat it as compromised.

The priority order is:

1. stop further publication of the exposed value;
2. inform the user immediately;
3. identify which credential must be rotated;
4. do not ask the user to send the replacement secret in chat;
5. ask the user to replace the credential locally or in the relevant system;
6. wait for confirmation that rotation is complete;
7. update dependent configuration when necessary;
8. restart or reconnect affected services when applicable;
9. verify functionality with the new credential;
10. handle Git history cleanup separately.

Credential rotation has higher priority than history cleanup.

Deleting the secret from the latest file version does not make the exposed credential safe again.

## 14. Push Permission Boundary

### 14.1 Explicit Approval Required

Do not push to the remote repository unless the user explicitly requests a push.

A local commit does not imply permission to push.

If push permission was not provided, report this clearly in the final task handoff.

Example:

```text
Commit: completed
Push: not performed — explicit user approval was not provided
```

### 14.2 Other Remote Mutations Requiring Approval

Also require explicit user approval before:

- creating a Pull Request;
- merging a Pull Request;
- deleting a remote branch;
- publishing a remote tag;
- creating or publishing a Release;
- manually triggering a GitHub Actions workflow;
- rerunning a workflow;
- cancelling a workflow;
- triggering deployment;
- changing repository settings;
- changing repository visibility;
- changing the default branch;
- changing repository access;
- adding collaborators;
- changing branch protection or rulesets;
- modifying repository or environment secrets.

## 15. Remote Synchronization Before Push

Before push:

1. fetch the current remote state;
2. compare the local branch with its remote counterpart;
3. determine whether local and remote histories are aligned, ahead, behind, or diverged.

Use an appropriate fetch operation before deciding that a push is safe.

Even for a private repository used by a single user, retain this check as protection against:

- web-based GitHub edits;
- pushes from another machine;
- another repository copy;
- future automation;
- accidental remote divergence.

If the remote contains unexpected commits, do not overwrite them automatically.

## 16. Force Push Safety

Do not use:

```bash
git push --force
git push -f
```

without explicit user approval.

Force push can overwrite remote history.

If rewriting remote history is explicitly approved and technically necessary, prefer:

```bash
git push --force-with-lease
```

because it provides protection against unexpectedly overwriting newer remote changes.

Do not force-push when the remote state is not fully understood.

## 17. Pull, Rebase, Merge, and Conflicts

Do not automatically perform history-changing reconciliation when local and remote branches have diverged unless the correct strategy is clear and safe.

Do not blindly run operations such as:

```bash
git pull --rebase
```

and automatically resolve meaningful conflicts.

When conflicts occur:

- inspect the conflicting changes;
- preserve user work;
- resolve only clearly scoped and unambiguous conflicts within the current task;
- escalate architectural, behavioral, or unrelated-user conflicts to the user before choosing a resolution.

Do not discard remote or local work to make synchronization easier.

## 18. Pull Requests

### 18.1 Creation

Create a Pull Request only when the user explicitly requests one.

Do not create PRs automatically for routine work on `main`.

### 18.2 PR Content

When a PR is requested, use a concise English title and a structured technical body.

Recommended body:

```md
## Summary

## Changes

## Verification

## Notes / Risks
```

Include only relevant sections.

### 18.3 PR Checks

When a PR exists, reading its status, checks, review state, or CI results is allowed without additional approval when needed for diagnosis or verification.

Merging the PR requires explicit user approval.

## 19. GitHub Actions and CI

### 19.1 Read-Only Inspection

The agent may inspect GitHub Actions and CI state when useful.

This includes:

- workflow runs;
- CI results;
- failed checks;
- job logs;
- PR checks.

Use `gh` when available and authenticated.

### 19.2 Workflow Mutations

Require explicit user approval before:

- manually starting a workflow;
- rerunning a workflow;
- cancelling a workflow;
- dispatching a deployment workflow;
- performing another action that can mutate repository or deployment state.

Do not assume a workflow is harmless merely because it is named as a test or CI workflow.

## 20. Repository Privacy and Access

### 20.1 Preserve Private Visibility

If a repository is private, it must remain private unless the user explicitly requests a visibility change.

Do not make a private repository public.

### 20.2 Preserve Access Restrictions

Do not add collaborators, teams, deploy keys, external users, or other repository access without explicit user approval.

Do not broaden access permissions automatically.

### 20.3 Repository Settings

Do not modify repository settings unless explicitly requested.

This includes:

- visibility;
- default branch;
- branch protection;
- rulesets;
- Actions permissions;
- environments;
- repository secrets;
- webhook configuration;
- collaborator access;
- other security-sensitive settings.

## 21. Releases, Tags, and Issues

Reading repository metadata, existing tags, releases, or issues is allowed when relevant.

Creating or mutating remote GitHub objects requires explicit user approval when the action changes repository state.

Examples requiring approval:

- publishing a tag;
- creating a Release;
- editing a Release;
- creating or closing an Issue on the user's behalf;
- modifying labels or milestones when not explicitly requested.

## 22. GitHub Operation Fallbacks

When a requested GitHub-specific task cannot be completed through authenticated `gh`:

1. explain what is unavailable;
2. offer installation or authentication of `gh`;
3. provide a regular Git alternative when applicable;
4. provide concise manual GitHub UI steps when the action cannot be performed through Git alone.

Do not present GitHub CLI as mandatory for basic Git repository work.

## 23. Task Handoff

At the end of a Git/GitHub-related task, report all applicable repository operations clearly.

Include:

- current branch;
- commit created or not created;
- commit hash when a commit was created;
- commit message;
- whether unrelated user changes remain locally;
- whether push was performed;
- whether push was skipped because explicit approval was absent;
- remote synchronization result when push was requested;
- PR status when applicable;
- CI / GitHub Actions result when checked;
- unresolved repository risks or conflicts.

Do not imply that a remote operation occurred when it was not performed.

## 24. Definition of Done

A Git/GitHub-related task is complete only when all applicable conditions are satisfied:

- repository state was inspected;
- current branch was verified;
- `main` policy was respected unless the user explicitly established another branch;
- pre-existing user changes were identified;
- manual user changes were not included without approval;
- destructive Git commands were avoided unless explicitly approved;
- the task-related diff was reviewed;
- `.gitignore` was checked;
- sensitive and generated files were reviewed;
- no real secrets were staged or committed;
- only task-related or explicitly approved changes were staged;
- staged diff was reviewed before commit;
- the task scope was in a coherent and appropriately verified state;
- the commit uses Conventional Commits;
- the commit message is in English;
- Git identity was configured appropriately;
- remote Git transport uses SSH when applicable;
- `gh` authentication was checked for GitHub-specific operations when relevant;
- remote state was fetched and checked before push;
- push occurred only with explicit user approval;
- force push was not used without explicit approval;
- PR creation or merge occurred only with explicit user approval;
- GitHub Actions mutations occurred only with explicit user approval;
- private repository visibility was preserved;
- repository access was not broadened without approval;
- GitHub credentials or other secrets were not exposed;
- any pushed secret was treated as compromised and rotation was prioritized;
- skipped approval-gated operations were reported explicitly;
- remaining local user changes were reported when relevant.

If any applicable check could not be performed, mark it explicitly as unverified rather than treating the repository operation as fully validated.
