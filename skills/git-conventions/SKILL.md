---
name: git-conventions
description: Use this skill before creating a git branch, writing a commit message, updating CHANGELOG.md, or opening a merge/pull request. Covers branch naming, conventional commits, changelog entries, and MR/PR requirements such as always opening them in draft state.
metadata:
  revision: 1
  status: experimental
  optional_dependencies:
    - glab # GitLab CLI, preferred for GitLab operations
    - gh # GitHub CLI, preferred for GitHub operations
---

# Git Conventions

Shared git and merge request conventions.

**Precedence:** if the repository's own `AGENTS.md` or `docs/**/git-conventions.md` states a different rule for the same topic, the repository-local rule wins. These are the defaults.

## Branch Naming

- **With a ticket (preferred):** use the ticket ID as the branch name, e.g. `PROJ-1234`.
- **Without a ticket:** `username/type-short-description`
  - `username` is your lowercase first name only (e.g. `alice`, not `alice.smith`).
  - `type` is one of the commit types below.
  - Hyphen-separated, concise, lowercase description.

```
PROJ-1234
alice/feat-add-popover-animation
bob/fix-correct-button-padding
carol/chore-update-dependencies
```

Always branch from the default branch (`main`). Never push directly to `main`.

## Commit Messages

Follow conventional commits:

```
type(scope): description [TICKET-ID]
```

- **type**: `feat`, `fix`, `chore`, `refactor`, `docs`, `test`, `style`, `ci`, `perf`
- **scope**: component, module, or area of change (e.g. `Button`, `search`, `deps`)
- **description**: brief, imperative
- **[TICKET-ID]**: append the ticket ID when one exists

Make focused, atomic commits: one logical change per commit.

```
feat(Popover): add standardMode override support [PROJ-1234]
fix(search): handle empty search results gracefully [PROJ-1235]
chore(deps): update @react-aria packages
```

## Secrets

- Never commit secrets. Create `.env` from `.env.example` (`.env` is gitignored).
- Shared credentials live in your team's password manager, never in the repository.
- Never print tokens or environment variables in command output, logs, commit messages, or MR descriptions.

## Updating the Changelog

For any user-facing change (new feature, API change, bug fix, deprecation, removal), update `CHANGELOG.md` if the repository has one:

1. Add the entry under `## [Unreleased]` (create the section if missing).
2. Place it under the matching subsection, keeping subsections in alphabetical order:
   - `### Added`: new features, components, props
   - `### Deprecated`: APIs marked for future removal
   - `### Fixed`: bug fixes
   - `### Removed`: deleted features or breaking changes
   - `### Updated`: changes to existing functionality
3. Use the format `- **Name**: description`, where `Name` is the component, package, or area.
4. Write concisely, focusing on user impact.

```markdown
## [Unreleased]

### Added

- **Button**: Added `isLoading` prop with spinner animation

### Fixed

- **Modal**: Fixed focus trap not releasing on unmount
```

## Creating Merge Requests

**Always open MRs/PRs in draft state** (`glab mr create --draft ...` or `gh pr create --draft ...`).

1. Use the repository's MR/PR template (e.g. `.gitlab/merge_request_templates/` or `.github/pull_request_template.md`) when present. Fill in every section. Do not leave raw placeholders.
2. Replace the summary placeholder with a concise description of what the MR does.
3. Link the ticket, e.g. `[PROJ-1234](https://<your-tracker>/browse/PROJ-1234)`.
4. Check off completed checklist items.
5. Add a "How to test" section with concrete steps when applicable.
6. Assign the MR to the user who asked for it. When the request comes from a bot or automation, also assign the human driving the work, typically the ticket's assignee when available.

**Before creating the MR**, run the repository's verification commands listed in its `AGENTS.md` (tests, lint, build) and make sure they pass.

### Screenshots for user-visible changes

When an MR touches rendered UI (components, styles, layout, docs pages), include a `## Screenshots` section: a before/after pair when modifying existing UI, or a single "after" shot for new UI. Capture them using Chrome DevTools or a similar applicable MCP available in your session. Highlight the relevant area and include a source link per image when useful. A description is not a substitute for the screenshot. This applies to both human and automated review.
