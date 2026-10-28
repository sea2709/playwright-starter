---
name: create-playwright-test
description: Use when turning a plain-language test case into a Playwright Test (TypeScript) in the playwright-starter project for Dang's CareerForce Drupal. Use the existing Page Objects, test-data helpers, and auth setup — do not invent new patterns.
---

# Create a Playwright test from a test case

You are turning a plain-language test case (see the `write-test-case`
skill) into code. If the case is missing the user role, the starting URL,
or the expected visible result, ask for those before writing code. Do not
guess them, and do not guess selectors, URLs, or content types.

## First, read the existing project

Before writing anything, look at what already exists and reuse it:

- `playwright.config.ts` — projects: `setup` (login once), `anonymous`
  (read-only smoke in `tests/smoke/`, no login), and `chromium`
  (authenticated tests, reuses the saved session).
- `tests/pages/` — Page Objects for the CareerForce smoke suite (home,
  search, listings, locations, tools, login, and others). Reuse a Page
  Object if one already covers the page. Only create a new Page Object
  when the flow is repeated or complex enough to need one.
- `tests/helpers/test-data.ts` — test-data helpers (for example
  `createTestArticle` / `deleteTestArticle`). Reuse them or follow the
  same pattern for other content types.
- `tests/auth.setup.ts` — login once via Drush one-time login, saved to
  `playwright/.auth/editor.json`. Do not log in again inside a test.

## Where the new test goes

- **Anonymous and read-only** (viewing, searching, listing pages):
  put it in `tests/smoke/` so it runs in the `anonymous` project. It must
  need no login and create nothing.
- **Authenticated or destructive** (create, edit, delete, admin pages):
  put it in `tests/` (for example `tests/<feature>.spec.ts`). It runs in
  the `chromium` project with the saved editor session, on QA/staging
  only. Never point destructive tests at production.

## Strict rules

- Use `page.getByRole`, `getByLabel`, and `getByText` — not brittle CSS
  or XPath copied from the rendered page. Prefer stable URLs
  (`/node/add/article`, `/admin/content`) over clicking through admin
  menus where possible.
- No `page.waitForTimeout()` and no `waitForLoadState('networkidle')`.
  Drupal's BigPipe and AJAX make both flaky. Wait for the specific
  visible thing with a web-first assertion, for example
  `await expect(locator).toBeVisible()`.
- For CKEditor, click the `.ck-editor__editable` area and type; do not
  fill a hidden textarea. For autocomplete, type, wait for
  `getByRole('option')`, then click it. For dialogs, use
  `getByRole('dialog')`.
- One test, one journey. Keep it short enough to stay green.
- Create its own test data with a unique title, and delete it in a
  `finally` block — even if an assertion fails. Read-only tests create
  nothing and need no cleanup.
- No hardcoded staging/production URLs, usernames, or passwords. Use
  `process.env.BASE_URL` and the saved session. Never commit `.env` or
  `playwright/.auth/`.
- Assertions check what a user can see (a heading, a message, a listing),
  not database values or API status codes, unless the case specifically
  asks for that.

## Before you call the test done

1. Run the single new test: `npx playwright test <path-to-test>`.
2. Run it a second time, to catch a flaky test early.
3. Run `npm run lint` and `npm run typecheck` and fix what they flag.
4. Hand the test to QA to verify with the `verify-playwright-test`
   skill — QA watches the trace/video and confirms it matches the manual
   test. A test is not done when the code looks right; it is done when
   it runs green twice and QA confirms the behaviour.
