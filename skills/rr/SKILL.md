---
name: rr
description: Fetch new automated pull request review comments, address actionable items, reply with the outcome, and push the updates.
---

# Review Response

Handle new automated review comments on a pull request, such as comments from GitHub Copilot or similar review agents. The goal is to quickly address valid and actionable feedback, keep the pull request moving, and leave a clear response trail for each comment.

## Procedure

1. Use the available review comment fetching workflow, such as `comment-fetcher`, to collect new automated review comments from the pull request.
2. For each comment, classify it into one of the following categories:
  - **actionable change needed**
  - **explanation only**
  - **clarification needed**
  - **no change planned**
3. Prepare a concise response plan that includes:
   - the code changes to make, if any
   - the reply to leave for each comment
4. If a comment would require a substantial change, risky refactor, or a design decision change, get approval from the user before proceeding.
5. Implement the planned fixes for actionable comments.
6. Validate the changes by running relevant tests, checks, or manual review as appropriate.
7. Reply to every reviewed comment with the result:
   - what was changed
   - why no change was made
   - what clarification is needed
8. Commit the changes with clear commit messages, push the branch, and update the pull request as part of the normal workflow.
9. If the reviewer is an automated agent (e.g., Copilot), re-assign the pull request back to the agent for further review after pushing the changes. This is only necessary if the agent has provided actionable feedback that was addressed, to trigger a new review cycle.
10. If the target PR is stacked and other PRs depend on it, restack the dependent PRs after pushing the changes to ensure they are based on the latest code. See `gh-stack` skill for details on restacking.

## Best Practices

- Do not ignore comments silently.
- Prefer small, targeted fixes that directly address the comment.
- If no code change is needed, still reply with a brief explanation.
- Ask for clarification only when the correct action cannot be determined from the code, context, or comment.
- Keep the pull request focused; comments that are out of scope may be acknowledged without expanding the change unnecessarily.
- Avoid squashing commits during this process so review-related changes remain easy to inspect.