# Local mode

In local mode, the review loop requests a review from Codex CLI and reads its comments. Reviews and fixes stay in the local working tree.

## Placeholder

In this document, `REPO_PATH` and `REVIEW_FILE` are placeholders for the absolute repository path and the current cycle's review result file, respectively. Replace them with the actual values when executing commands. Use a new review result file outside the repository for each cycle.

## Loop definition

Local mode loops through the following steps:

1. Request a review from Codex
2. Read review comments
3. Filter new comments and exit the loop if possible
4. Address actionable comments
5. Record the outcome of all comments
6. Return to step 1 if further review is needed

### 1. Request a review from Codex

Start a new Codex review of staged, unstaged, and untracked changes. Validate the repository path before starting. If a review of the current working tree is already running, wait for it and proceed to step 2. Keep the working tree unchanged while the review is running.

```bash
codex --ask-for-approval never exec \
  --cd "REPO_PATH" \
  --sandbox read-only \
  --output-last-message "REVIEW_FILE" \
  review --uncommitted
```

### 2. Read review comments

Wait for Codex to finish successfully, then read the current cycle's result. If the review fails or the result is missing or incomplete, stop and report the issue.

```bash
cat "REVIEW_FILE"
```

### 3. Filter new comments and exit the loop if possible

For each comment, check if it is actionable. Actionable comments should satisfy the following conditions:

- It is not addressed in a previous cycle.
- It is not a duplicate of another comment or a comment that has already been addressed.
- It mentions a real issue in the code, such as a bug, a missing feature, or a violation of coding standards.

The following are examples of non-actionable comments:

- Comments that request complex changes for backward compatibility.
- Comments that request out-of-scope changes, such as new features unrelated to the current changes.
- Hallucinated comments that refer to non-existent variables or functions.
- Comments about hypothetical edge cases whose triggering conditions do not apply to the code.

Compare comments across cycles by the affected code and underlying issue. A recurring issue that remains in the code is still unresolved.

If there are comments that are difficult to classify, ask the user for clarification.

If no actionable comments remain, record why the remaining comments do not require changes and exit the loop. Provide a final report including validation results and any unresolved comments or issues.

### 4. Address actionable comments

Address comments that are classified as actionable. For each actionable comment, plan the changes and implement them locally. Validate the changes to ensure they address the comment and do not introduce new issues. Follow the repository's development workflow and preserve unrelated user changes.

Leave the changes uncommitted so the next review includes the fixes.

### 5. Record the outcome of all comments

Record the outcome locally for all comments, including those that are not actionable. For comments that are not actionable, provide a clear explanation of why the comment is not addressed. For actionable comments, record the changes made and their validation results.

### 6. Return to step 1 if further review is needed

Return to step 1 if actionable comments were addressed in the current cycle. Review the updated working tree before declaring the loop complete.

If the same unresolved issue recurs without progress or validation cannot be completed, stop and report the remaining issues. Respect any cycle limit specified by the user.

## Rules

- Do not push, fetch, pull, create or update pull requests, post comments, or trigger remote reviews. Do not use `gh` or Codex cloud tasks.
- Do not commit, amend, rebase, or otherwise rewrite Git history during this mode.
- The parent agent applies fixes; the Codex reviewer remains read-only and must not invoke this loop recursively.
- Keep review results and comment outcomes outside the repository.
- Explanations should be short, clear, and concise.
