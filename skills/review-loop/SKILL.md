---
name: review-loop
description: Run repeated code reviews and address actionable feedback using GitHub Copilot for pull requests or Codex CLI for local changes.
disable-model-invocation: true
model: best
effort: medium
---

# Review Loop

Select the mode from the invocation argument and read the corresponding reference before starting. Follow its review loop and rules. Default to `copilot` when no mode is specified.

## Invocation

- `review-loop copilot` or `review-loop`: Use [GitHub Copilot mode](references/github-copilot-mode.md) to review a pull request, address comments, commit and push fixes, and reply to inline comments.
- `review-loop local`: Use [Local mode](references/local-mode.md) to review local changes with Codex CLI, address comments, and validate fixes without committing, pushing, or performing GitHub operations.

If an unsupported mode is specified, ask the user to choose `local` or `copilot` before starting.

## Fallback

GitHub Copilot may not be available due to token limits. In that case, ask the user to switch to `local` mode and continue the review loop. This fallback is also available during the review loop.