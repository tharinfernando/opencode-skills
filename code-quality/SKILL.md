---
name: code-quality
description: Use when implementing or reviewing a code change.
---

# Code Quality

Keep changes small, conventional and verifiable.

## Rules

1. Match the existing conventions, structure and libraries. Do not introduce a new pattern or dependency unless the user asks.
2. Keep the diff minimal. No drive-by refactors and no unrelated formatting.
3. Handle errors explicitly. Do not swallow exceptions and do not leave empty catch blocks.
4. Leave no dead code, commented-out code, stray `console.log` or leftover TODO.
5. Add or update tests for new logic, including failure paths, not only the happy path. Run them as described in `testing-before-push`.
6. Validate input at boundaries: API routes, Edge Functions and forms. Never trust client data.
7. Do not claim "done" or "fixed" without verification evidence.
8. If a requirement is ambiguous, ask or state the assumption instead of guessing.
9. Before finishing, review your own diff as if you were the reviewer. Check the reported change against the actual files.
