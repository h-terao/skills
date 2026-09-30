---
name: gh-pr
description: Create a GitHub pull request with the gh CLI, including attaching images or videos such as screenshots to the pull request body.
---

# GitHub Pull Request

Create a pull request for the current branch with `gh pr create`.

## Procedure

1. Confirm the base branch. Use the base branch stated by the user, or the repository's default branch if none is given.
2. Inspect the changes with `git log <base>..HEAD` and `git diff <base>...HEAD`.
3. Push the branch with `git push -u origin <branch>` if it is not pushed yet.
4. Write a title that briefly describes the change, and a body that a colleague without the conversation context can understand.
5. If screenshots or videos help explain the change, attach them with `--attach`.
6. Create the pull request and report the printed URL to the user.

## Style

- Write a title that briefly describes the change.
- Do not use terms defined in the plan or conversation in the description. Write it so that a colleague without the context can understand it.
- Do not mention approaches that were not adopted or problems found and fixed during the implementation in the description.
- Keep it clear and concise. Do not include unnecessary information.
- If frontend changes are included, attach screenshots or videos to help reviewers understand the change.

## Commands

### Create a pull request

```bash
gh pr create --base <base> --title "<title>" --body-file <body.md>
```

- `--body-file -` reads the body from standard input.
- `--draft` creates the pull request as a draft.
- `--dry-run` prints the details without creating the pull request. It may still push git changes.

### Attach images or videos

`--attach` uploads an image or video file and adds it to the body. It is available on `gh pr create`, `gh pr edit`, and `gh pr comment`.

```bash
gh pr create --title "<title>" --body-file <body.md> --attach './login.png#The login error state'
gh pr create --title "<title>" --body-file <body.md> --attach ./before.png --attach ./after.png
gh pr edit <number> --attach './login.png#The login error state'
gh pr comment <number> --attach ./demo.mp4
```

- The format is `'<file>#<image alt text>'`. Without `#<alt text>`, the filename is used as the alt text.
- Repeat the flag to attach multiple files. Up to 50 files can be attached per command.
- Attachments not referenced in the body are appended to the end of the body.
- A body reference to an attached file, such as `![alt](./login.png)`, is rewritten to point at the uploaded asset. The alt text written in the body is kept. Use this to place an image at a specific position in the body.
- Videos render as a player and cannot have alt text.
- `gh pr edit` without a body flag keeps the existing body and appends the attachments to it.
- If some uploads fail, the pull request is still created or updated with the successful ones. The command exits with a non-zero status, but the URL is still printed to stdout. Check the result and retry the failed files with `gh pr edit <number> --attach`.
