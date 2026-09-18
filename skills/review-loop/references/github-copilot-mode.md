# GitHub copilot mode

In GitHub Copilot mode, the review loop requests a review from GitHub Copilot and fetches its comments.

## Placeholder

In this document, `PR_NUMBER`, `OWNER`, and `REPO` are placeholders for the pull request number, repository owner, and repository name, respectively. Replace them with the actual values when executing commands.

## Loop definition

GitHub Copilot mode loops through the following steps:

1. Request a review from GitHub Copilot
2. Fetch review comments
3. Filter new comments
4. Address actionable comments
5. Commit and push changes
6. Reply to all inline comments
7. Return to step 1 if further review is needed

### 1. Request a review from GitHub Copilot

Register GitHub Copilot as a reviewer on the pull request and request a review.
First check currently running reviews to avoid duplicate requests.
If a review of the latest commit is already running, skip this step and proceed to step 2.
Otherwise, request a review from GitHub Copilot using the following command:

```bash
gh pr edit PR_NUMBER --repo OWNER/REPO --add-reviewer "@copilot"
```

### 2. Fetch review comments

Run the following command to fetch all review comments for the pull request:

```bash
gh api --paginate "repos/OWNER/REPO/pulls/PR_NUMBER/comments?per_page=100" --jq '.[] | select(.id > LAST_COMMENT_ID)'
```

, where `LAST_COMMENT_ID` is the highest comment ID fetched in previous cycles. If no comments are returned, retain the previous value of `LAST_COMMENT_ID`. Otherwise, update it to the highest fetched comment ID as

```bash
gh api --paginate "repos/OWNER/REPO/pulls/PR_NUMBER/reviews?per_page=100"
```

### 3. Filter new comments and exit the loop if possible

For each comment, check if it is actionable. Actionable comments should satisfy the following conditions:

- It is not addressed in a previous cycle
- It is not a duplicate of another comment or a comment that has already been addressed
- It mentions a real issue in the code, such as a bug, a missing feature, or a violation of coding standards.

Belows are examples of non-actionable comments:

- Comments that request complex changes for backward compatibility.
- Comments that request out-of-scope changes, such as new features or changes that are not related to the current pull request.
- Hallucinated comments that are not based on the actual code, such as comments that refer to non-existent variables or functions.
- Edge cases where the conditions for too rarely occurring bugs are not met, such as a comment that requests a fix for a bug that is unlikely to occur in practice.

You should also classify suppressed comments. Note that suppressed comments are non-actionable in default, but you may choose to address them if they are valid, relevant and critical. Suppressed comments are those that are hidden by GitHub Copilot due to their low confidence or relevance.

If there are comments that are difficult to classify, ask the user for clarification using the AskUserQuestion tool.

### 4. Address actionable comments

Address comments that are classified as actionable. For each actionable comment, plan the changes and implement them. Validate the changes to ensure they address the comment and do not introduce new issues.

### 5. Commit and push changes

Commit the changes and push them to the pull request. Use a clear and concise commit message that describes the changes made.

### 6. Reply to all inline comments

Reply to all inline comments, including those that are not actionable. For comments that are not actionable, provide a clear explanation of why the comment is not addressed. For comments that are actionable, provide a clear explanation of the changes made to address the comment.

Note that you do not need to reply suppressed comments including those that are classified as actionable. You only add commits for them, but no reply is needed.

### 7. Return to step 1 if further review is needed

You return to step 1 if you have found actionable comments in the current cycle. Otherwise, exit the loop and provide a final report of the review process, including any unresolved comments or issues.

Note that, if you have found actionable comments only from suppressed comments, exit the loop. This is a team decision to avoid unnecessary review cycles and to focus on the most relevant comments. You can still address suppressed comments, but you do not need to request another review from GitHub Copilot.

## Rules

- You should not post comments except for replies to inline comments. No other comments should be posted to the pull request, such as general comments or comments that are not related to the code.
- Comments should be short, clear and concise.
- Only use general words in comments. Do not include words defined in our plans or conversations. Other team members does not know our context.