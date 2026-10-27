---
name: verify-playwright-test
description: Use when a new or changed Playwright test in playwright-starter needs verifying before merge, or when a test fails and the team needs to understand why. Covers running the test, reading the report and trace, and deciding whether it is a site bug or a test bug.
---

# Verify a Playwright test

A test is verified when it runs green, it is stable on re-run, and a
human confirms it tests the right behaviour. Code that looks right is
not enough.

## Steps

1. **Run just the new or changed test first:**
   ```bash
   npx playwright test <path-to-test>
   ```
2. **Run it a second time.** A test that passes once and fails once is
   flaky. Flaky tests get fixed or removed — they do not get merged
   with a note saying "it usually passes".
3. **Run lint and typecheck:**
   ```bash
   npm run lint
   npm run typecheck
   ```
   Fix what they flag. Do not bypass them to get a test in.
4. **Open the report and check the behaviour:**
   ```bash
   npx playwright show-report
   ```
   For a new test, QA (or the case author) watches the trace/video and
   confirms it matches what was tested manually: same steps, same
   visible result. If the trace shows different steps or a different
   result, the test is wrong even if it is green.

## Reading a failure

In the report, click the failed test and look, in order:

1. Which step failed, and what was expected?
2. What did the page actually show? Check the screenshot.
3. What happened just before the failure? Use the trace replay.

Then decide which kind of failure it is:

- **Site bug:** the same journey also fails when you do it manually in
  a browser. Report it as a site bug, with the manual steps. Do not
  change the test to match broken behaviour.
- **Test bug:** the journey works manually, but the test fails. Common
  causes are a wrong selector, a wrong expected text, missing test
  data, or a timing assumption. Fix the test, not the site.
- **Environment problem:** the QA site is down, Basic Auth changed,
  `.env` is missing, or the test user has no permission. Check the
  setup in the `run-playwright-tests` skill before changing test code.

## Review checklist — all boxes before merge

- [ ] Runs green on QA, first time and on re-run
- [ ] Creates its own unique content and deletes it in `finally`
      (read-only tests: creates nothing)
- [ ] Uses `getByRole` / `getByLabel`, no sleeps, no `networkidle` wait
- [ ] No committed secrets, no hardcoded URLs, `.env` and `.auth/`
      gitignored
- [ ] Destructive steps are QA/staging-only; production gets read-only
      smoke only
- [ ] QA watched the trace/video and confirmed it matches the manual test
- [ ] Lint and typecheck pass

## What not to do to make a test pass

- Do not add `page.waitForTimeout()` to "wait a bit longer".
- Do not skip the test (`test.skip`) or focus it (`test.only`) in a
  commit.
- Do not weaken the assertion until it passes without checking why it
  failed.
- Do not commit `.env`, `playwright/.auth/`, or local report output.

If a test cannot be made stable, say so plainly and hand it to a
developer with the report and trace. An honest "this test is flaky and
we do not know why yet" is better than a green run bought with a sleep.
