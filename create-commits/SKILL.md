---
name: create-commits
description: Use when staging changes or writing a Git commit message.
---

# Create Commits

Testing, evidence, approval, branch, secrets and em-dash checks are in `testing-before-push`. This skill covers only staging scope and the commit message.

## 1. Confirm the gate

Before staging, confirm the `testing-before-push` gate is complete: the required tests ran, the evidence was shown, and the user approved staging and committing. If any part is missing, stop and follow that skill.

## 2. Stage only the change

```bash
git status --short
git diff
git branch --show-current
```

- Stage only files directly relevant to this change.
- Do not stage unrelated edits, generated files, screenshots, local-only documents or test artifacts.
- Run the secrets check in `testing-before-push` before staging.

## 3. Write the message

Use an imperative header and an optional body:

```text
fix(auth): correct token validation

Reject expired tokens before the session lookup. The previous order
returned a cached user for an expired token.
```

- Header: short, imperative, under 72 characters.
- Body: what changed and why, if the header is not enough.
- Run the em-dash check from `testing-before-push`.
- Keep one logical change per commit.

## 4. Commit

- Follow the approval order in `testing-before-push` before committing.
- Do not open a PR automatically after committing. Ask first and follow `create-pull-requests`.
- If the work is a proposal, spec, design note or local-only document, ask whether it should be committed at all.
