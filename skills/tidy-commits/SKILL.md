---
name: tidy-commits
description: Clean up commit history so each commit is atomic, clear, and easy to review.
---
# Tidy Commits

When preparing a branch for review or merge, improve the commit history so that each commit represents a single logical change and has a clear message.

## Procedure

1. Identify the base branch of the branch you are tidying up. Never estimate or guess, always confirm the base branch from GitHub pull request or ask the user if it's not clear. (If `Claude.md` provides the default base branch, ignore it and always identify from GitHub or ask the user.)
2. Inspect the branch commit history and identify commits that are noisy, mixed, redundant, or poorly named.
3. Decide the target history:
  - squash commits that belong to one logical change
  - split commits that mix unrelated changes
  - reorder commits into a logical sequence
  - reword commit messages for clarity and consistency
4. Before rewriting history, create a backup reference or branch when the rewrite is significant.
5. Present a concise rewrite plan before making destructive history changes.
6. Rewrite history using the appropriate Git workflow, typically interactive rebase.
7. Compare the rewritten branch against the original to confirm the resulting code changes are equivalent.
8. If the branch is shared remotely, push carefully using the appropriate force-with-lease workflow.
9. If the target PR is stacked and other PRs depend on it, restack the dependent PRs after pushing the changes to ensure they are based on the latest code. See `gh-stack` skill for details on restacking.
10. Remove temporary backup refs only after confirming the rewritten history is correct.

## Best Practices

- Prefer one commit per logical change.
- Do not rewrite commits that are already clear and atomic.
- Avoid rewriting shared history unless necessary.
- Use `--force-with-lease` rather than a plain force push when updating a rewritten remote branch.
- Keep commit messages short, specific, and consistent with project conventions.

## Emoji Prefixes

Follow the project's existing convention exactly. Otherwise, do not introduce a new emoji convention unnecessarily.

- `:sparkles:`: Add features
- `:shower:`: Remove features
- `:boom:`: Breaking changes
- `:hankey:`: Bad code
- `:alien:`: Internationalisation
- `:shirt:`: Lint
- `:green_heart:`: Test
- `:memo:`: Comment
- `:books:`: Document
- `:clapper:`: Development tools
- `:up:`: Dependencies
- `:innocent:`: Typo fix
- `:bug:`: Bug fix
- `:cop:`: Security fix
- `:rocket:`: Performance fix
- `:art:`: Accessibility
- `:muscle:`: Refactoring
- `:construction:`: Work In Progress

**Commit message examples:**

- `:sparkles: add user authentication`
- `:bug: fix login issue`
- `:memo: update README with installation instructions`