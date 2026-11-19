---
name: workflow
description: Use this skill at the start of any coding task in a repository. Covers workflow and behaviour rules: tool preferences for issue trackers, wikis, and git hosting, handling missing tools, external or destructive writes, judgment calls, reuse, precedence, checks before pushing, handling secrets, and writing code comments.
metadata:
  revision: 1
  status: experimental
  optional_dependencies:
    - glab # GitLab CLI, preferred for GitLab operations
    - gh # GitHub CLI, preferred for GitHub operations
---

# Workflow

Workflow and behaviour rules for working in a repository. For branching, commits, changelog, and MRs, also load `git-conventions`.

## Tool Preferences

- **Prefer CLIs over MCPs.** When both a CLI and an MCP server exist for the same system (issue tracker, wiki, git host), use the CLI. Fall back to the MCP only when the CLI is unavailable or fails, and tell the user why.
- **Git hosting:** use `glab` for GitLab or `gh` for GitHub. Check `glab auth status` / `gh auth status` first.

## Behaviour Rules

1. **Missing tools.** If a required tool, MCP, or source is unavailable, stop and report it. Do not improvise a workaround or make partial changes.
2. **External or destructive writes.** For anything that writes outside the repository (production data, CMS, design tools, cloud storage) or is destructive, preview or dry-run it first and get explicit user confirmation. Publishing to external systems is a human step unless the user explicitly asked for it.
3. **Judgment calls.** Make the clear-cut changes, then stop and ask on judgment calls instead of choosing a default. Mark unresolved decisions with an explicit `TODO`.
4. **Reuse first.** Use the repository's own scripts, generators, and utilities. Check the README and existing code before building something new.
5. **Precedence.** The repository's `AGENTS.md` overrides these guidelines. When docs disagree with each other or with the code, cite both sources and don't choose silently. Never change a public contract only to match docs; ask first.
6. **Before pushing.** Run the repository's check and test commands. Review the diff after formatters run, because they can touch unrelated files.
7. **Temporary and generated files.** Clean up temporary files you create. Don't read large generated or build artifacts directly.

## Secrets

Anything a command prints lands in the session transcript, so treat command output as public.

1. **Never print secret values.** Don't `cat`, `sed`, `grep` or `head` secret-bearing files such as `.env`, `.npmrc`, CLI configs (e.g. `glab-cli/config.yml`, `gh/hosts.yml`), or credential stores. To inspect an env file, list key names only: `cut -d= -f1 .env`. To check that a variable is set, test it without printing: `[ -n "$VAR" ] && echo set`.
2. **Avoid commands that reveal credentials.** For example: `glab auth status -t`, `glab config get token`, `gh auth token`, `printenv`, bare `env`, `ps eww` (prints a process's environment), `git config --list` when credentials are in remotes, and `set -x` around token use.
3. **Don't extract tokens from CLI config.** Let the tool authenticate (e.g. `glab api`, `gh api`). If it can't do the job, ask the user to do that step instead.
4. **Don't trust regex redaction.** A pattern that doesn't match fails silently and prints everything (for example, BSD `sed` doesn't support `\|`). Don't read the value in the first place.
5. **If a secret is printed anyway,** say so at once, name the secret (not its value), and ask the user to rotate it.

## Code Comments

1. **Comment sparingly.** Add a comment only when the code cannot explain itself: non-obvious intent, a constraint, or a workaround. Do not restate what the code does.
2. **Keep it concise.** Get to the point, usually in one or two lines.
3. **Use JSDoc for modular functions.** Document exported or reusable functions with a short description followed by the applicable tags (`@param`, `@returns`, `@throws`, `@deprecated`, `@example`). Include an `@example` when usage is not obvious from the signature.
4. **Skip ticket references.** Do not mention tickets in comments, unless the code is a workaround or hack and the ticket explains why it exists or when it can be removed.

```ts
/**
 * Clamps a number to the inclusive range between `min` and `max`.
 *
 * @param value - The number to clamp.
 * @param min - Lower bound.
 * @param max - Upper bound.
 * @returns `value` limited to the range `[min, max]`.
 * @example
 * clampNumber(15, 0, 10); // 10
 */
export function clampNumber(value: number, min: number, max: number): number {
  return Math.min(Math.max(value, min), max);
}
```
