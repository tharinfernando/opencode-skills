---
name: create-pull-requests
description: Use when opening a pull request or writing a PR title or body.
---

# Create Pull Requests

Testing, evidence, approval and the rules for work that must not become a PR are in `testing-before-push`. This skill covers the PR template, checklist and wording.

## 1. Confirm a PR is wanted

- If the work is complete but no PR was requested, stop and ask. Finishing a task is not permission.
- Follow the PR gate in `testing-before-push` before opening anything.

## 2. Use the template

Look for the template in this order:

- `.github/PULL_REQUEST_TEMPLATE.md`
- `.github/PULL_REQUEST_TEMPLATE/`
- `PULL_REQUEST_TEMPLATE.md`

If a template exists, follow its structure and sections strictly.

## 3. Handle the checklist

- Go through every checklist item.
- Mark a completed item as checked with `[x]`.
- Leave an item you cannot satisfy or that does not apply unchecked as `[ ]`. Do not remove it.
- Do not mark an item checked without evidence.

## 4. Write the title and body

- Title: clear and imperative, for example `feat: add user authentication`.
- Body: a short summary of what changed and why, plus the verification evidence from `testing-before-push`.
- Answer every template prompt.
- Do not include internal backlog, planning, phase or ticket narration.

## 5. Formatting check

- Run the em-dash check from `testing-before-push` on the title and body.
- Run the secrets check from `testing-before-push` on the diff and description.

## 6. Open the PR

- Open only after the gate in `testing-before-push` is complete.
- Report the PR URL and the verification evidence.
