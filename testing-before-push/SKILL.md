---
name: testing-before-push
description: Use before staging, committing, pushing or opening a pull request in any repository.
---

# Testing Before Push

Single source of truth for testing, browse MCP use, the commit and PR gate, secrets and the test report. Commit formatting lives in `create-commits`. PR formatting lives in `create-pull-requests`. Test commands come from the repo itself: `AGENTS.md`, CI config, package scripts, then docs. When they conflict, CI wins.

## Decision table

| Change type | Required test |
|---|---|
| User-facing flow, page, component, auth screen | Browse MCP against the changed flow |
| Database schema, RLS, migration | pgTAP against a local Supabase |
| Edge Function or API | Real call with a known payload, verified status, body and side effect |
| Engine, worker, queue, cron | Unit or integration tests at the changed boundary |
| Docs only | Build, link check or docs validation |
| Deployment or config only | Config validation plus a health check of the deployed surface |

If a UI change cannot be browser-tested, stop and tell the user. Do not substitute a build, lint or unit test for a browser test on a UI change.

## 1. Order

1. Identify the change surface.
2. Run the required tests for that surface.
3. Show the evidence.
4. Ask for approval to stage and commit.
5. Stage only the relevant files.
6. Commit.
7. Open a PR only after a separate PR request and approval.

Never reverse this order. Never ask for approval before showing test evidence.

## 2. Approval and git rules

- `git add`, `git commit` and opening a PR each need the user's approval.
- An explicit instruction in the current message ("commit this", "open a PR") counts as approval for that action only.
- A PR request does not approve a new commit.
- Finishing a task is never approval.
- Check `git branch --show-current` before staging. Never commit directly to main or master. Create a branch first.
- Never force-push and never use `--no-verify` unless the user explicitly asks.
- Open a PR only when the work must merge into main. Never open a PR for proposals, specs, design notes or local-only docs.

## 3. Test by surface

Test commands come from the repo itself, in this order: `AGENTS.md`, CI config, package scripts, then docs. When they conflict, CI wins. If the repo has no documented test commands, ask the user. Do not guess. Never invent a command, URL, environment variable or deployment step that you did not find in the repo or CI. If you are guessing, say so.

- **UI:** run the exact changed flow through the browse MCP section below. Cover the happy path and at least one relevant failure or validation path.
- **Schema:** run the specific pgTAP file first when iterating, then the full suite. A headless browser suite is not schema coverage.
- **Edge Functions and APIs:** call the changed function with a known payload. Verify status code, response body, authorization and the database side effect. Do not substitute a browser test for backend coverage.
- **Engine and workers:** run unit and integration tests at the changed boundary. Exercise the failure path, not only success.
- **Docs:** run the docs build or link check. A browser test is not required.
- **Deployment:** validate the config and check the deployed health endpoint. Record the deployment ID.

Do not claim one surface was tested by a different kind of test.

## 4. Browse MCP

### Setup check

Run this before every browser test:

1. Call the browse `status` tool. Confirm the relay is connected, pairing is connected and a browser is available. If not, stop and tell the user.
2. List the browse tools exposed in the current session and compare their names and parameters with this section. The list is not permanent. If anything differs, stop and tell the user.
3. Create a session with `session_new` and only the exact local origin allowlisted. No wildcards.
4. Navigate to the local app URL and confirm `page.url()` and the page title. If the URL does not load, stop and tell the user.

If the browse MCP is unavailable or fails to start, stop and tell the user. Never fall back to the Playwright MCP or any other browser tool. Do not commit on build or lint alone for a UI change.

Never allowlist UAT or production origins unless the user approves it.

### Workflow

- Create the session with `session_new({ allowedOrigins: ["http://127.0.0.1:5173"] })`. Use only the exact local origin.
- Start `pageConsole.start()` before the flow.
- Navigate with `page.goto()`, inspect with `snapshot()`, and act on the returned refs such as `ref("e1").click()`. Use `page.getByRole()` or `page.getByLabel()` when a ref is unavailable.
- Verify every meaningful action with a fresh read: `page.url()`, a heading, or a new `snapshot()`. A click returning is not proof that it worked.
- Check persistence and the database side effect, not only a toast.
- Console messages appear in the `execute` result under `logs`, and `aftermath.consoleErrorCount` counts errors. `pageConsole.stop()` does not return them.
- Console errors on the final page fail the test, unless they also exist on main. In that case, report them separately as pre-existing.
- Write network and recording artifacts outside the repo. Never save them inside the repo and never stage them.
- Clean up temporary test users and rows.
- Reset or delete the session at the end with `session_reset` or `session_delete`, even when the test fails or times out.

Auth steps go through `handoff()`: login, 2FA, passkey, captcha and payment confirmation. Do not bypass them, automate around them or type real credentials. Wait for the user.

### Confirmed example

Run this in the browse `execute` tool:

```js
await pageConsole.start();
await page.goto("http://127.0.0.1:5173/");
const snap = await snapshot(); // "- link \"Learn more\" [ref=e1]"
const linkCount = await page.getByRole("link", { name: "Learn more" }).count();
await ref("e1").click();
const url = page.url();
pageConsole.stop();
return { url, linkCount, snap };
```

The console error count is in the `execute` result:

```json
{
  "logs": [{ "source": "page", "type": "error", "text": "..." }],
  "aftermath": { "consoleErrorCount": 1 }
}
```

### Evidence to record

- URL tested
- Exact actions performed
- Expected result
- Actual result
- Console errors
- Test data cleanup
- Session reset or deletion

### Edge cases to check when relevant

- Mobile viewport
- Loading state
- Empty state
- Error state
- Double submit
- Reload persistence
- Expired session
- Back button

## 5. Anti-gaming

- Never skip, delete, loosen or `.skip` a failing test, and never edit assertions just to pass.
- If a test is wrong, say so and ask before changing it.
- If lint, audit or tests fail, compare with main to find out whether the failure already existed. Report baseline failures separately and do not fix unrelated ones silently.
- If a test is flaky, rerun it once and report the flake. Never retry until it passes.
- For a bug fix, reproduce first with a failing test or a browser repro, then fix, then show the same check passing.

## 6. Supabase, RLS and migrations

- For every new table or policy, confirm RLS is enabled.
- Test as anon, as the owner, and as a different authenticated user. `service_role` bypasses RLS, so it is not coverage.
- Migrations must be idempotent, safe on existing data, and have a rollback plan noted.

## 7. Secrets

- Use the repo's configured scanner if one exists, from the pre-commit config or CI job. Otherwise use `gitleaks` if installed. Otherwise grep for common key and token patterns.
- Name the tool used in the report.
- Never put credentials, tokens or real user data in code, tests, screenshots or logs.

## 8. Em-dash check

Before committing or opening a PR, grep the commit message and PR body for the em-dash character, U+2014, and fix any hits. Use plain hyphens and colons.

## 9. Stop conditions

Do not commit, push or open a PR when:

- a test fails
- the exact changed flow was not tested
- a UI change was not browser-tested through the browse MCP
- the browse MCP is unavailable or failed to start
- console errors are present on the final page and are not pre-existing on main
- secrets or live credentials appear in the diff
- unrelated files are staged
- a migration was applied out of order without re-verification
- the user did not approve staging or committing
- the user did not ask for a PR
- the work is a proposal, spec, design note or other local-only document that does not need to merge

## 10. Report format

```text
Changed surface:
Exact flows tested:
Commands run:
Results:
Not tested / could not verify:
Assumptions made:
browse MCP evidence:
Database side effects:
Console errors:
Cleanup:
CI:
Deployment state:
```

Never claim everything passed if something was skipped.
