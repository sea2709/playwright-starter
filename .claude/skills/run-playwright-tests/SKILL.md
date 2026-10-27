---
name: run-playwright-tests
description: Use when a team member wants to run Playwright tests in the playwright-starter project for Dang's CareerForce Drupal — checking the project out, installing it, running one test or the anonymous smoke suite, or reading a test report. Written for QAs, BAs, and anyone new to the project.
---

# Run Playwright tests

This project is a separate Playwright project for Dang's CareerForce Drupal.
It is not inside the Drupal codebase. Tests run against the QA site by
default, not production. Destructive tests (create, edit, delete) are
staging/QA-only. The anonymous smoke suite is read-only and prod-safe.

## Before you start

You need, once:

1. Git, Node.js, and npm installed. If any of these is missing, ask a
   developer for help installing them — do not guess at installers.
2. The project checked out:
   ```bash
   git clone https://github.com/sea2709/playwright-starter.git
   cd playwright-starter
   ```
3. A `.env` file, copied from the example and filled in with the team's
   values. Never commit real credentials.
   ```bash
   cp .env.example .env
   ```
   Fill in `BASE_URL` (the QA site, currently `https://qa.careerforce.link`)
   and the HTTP Basic Auth user and password for the QA gate. Those are the
   same credentials you would type when opening the QA site in a browser.
4. Dependencies and the browser, once:
   ```bash
   npm install
   npx playwright install chromium
   ```

## Run tests

Use these commands from the project folder. Start with the first one.

```bash
# Anonymous, read-only smoke suite — the safest first run, no login needed:
npx playwright test --project=anonymous

# One test file, for example the home smoke test:
npx playwright test tests/smoke/home.spec.ts --project=anonymous

# Friendly visual runner — pick a test, watch it run, click into results:
npx playwright test --ui

# Full suite, including authenticated tests (QA/staging only):
npx playwright test
```

The full suite logs in once via `tests/auth.setup.ts` (Drush one-time login)
and reuses that session. If you do not have Drush set up locally, do not try
to work around it yourself — ask a developer about the UI-login fallback in
`tests/auth.setup.ts`. Never run the full suite against production.

## Read the report

After a run, open the HTML report:

```bash
npx playwright show-report
```

In the report:

- Green means the test passed. Red means it failed.
- Click a failed test to see which step failed, what the page looked like
  (screenshot), and, on retry, the trace — a step-by-step replay of the
  test, including what was on screen at each step.
- A test that passes sometimes and fails sometimes is called "flaky".
  Do not ignore it and do not add a sleep to make it pass. Note it down
  and hand it to a developer with the report link or trace.

## If a run fails at setup

Check, in this order:

1. Are you in the `playwright-starter` folder?
2. Is `.env` present and filled in, especially `BASE_URL` and Basic Auth?
3. Did `npm install` and `npx playwright install chromium` complete?
4. Is the QA site itself up in a browser?

If the site works in a browser but the test does not, share the exact
command you ran and the failure from the report — do not paste `.env`
contents or passwords anywhere.

## Never commit

- `.env` (real credentials)
- `playwright/.auth/` (saved login sessions)
- `test-results/` and `playwright-report/` (local run output)

All of these are already gitignored.
