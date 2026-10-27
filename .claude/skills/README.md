# Claude skills for playwright-starter

Skills are guidance for Claude, written for the whole team — including
QAs and BAs who do not write code. They live in this folder so everyone
working on this project gets the same instructions. A skill does not run
tests by itself; a person still runs the commands in a terminal, and
Claude follows the skill's steps.

## Skills in this folder

- **run-playwright-tests** — check the project out, install it, run one
  test or the anonymous smoke suite, and read a test report.
- **write-test-case** — write a test case in plain language, no code
  needed, before any test is written.
- **create-playwright-test** — turn a plain-language test case into a
  Playwright test, reusing the existing Page Objects, test-data helpers,
  and saved login session.
- **verify-playwright-test** — verify a new or changed test before
  merge, read a failure in the report and trace, and decide whether it
  is a site bug or a test bug.
- **plan-sprint-tests** — turn sprint tickets into a small, prioritised
  test plan, split into anonymous read-only and authenticated tests.

## How to use them

In Claude Desktop (or Claude Code) with this project open, just describe
what you want to do in plain words — for example, "run the smoke tests",
"write a test case for searching listings", or "plan tests for this
sprint". Claude should pick the matching skill. If it does not, name the
skill explicitly, for example: "Use the run-playwright-tests skill".

## Ground rules for the whole project

- Destructive tests (create, edit, delete) run on QA/staging only,
  never production. Production gets read-only smoke tests only.
- Never commit `.env` or `playwright/.auth/`.
- A test is done when it runs green twice and QA confirms its trace
  matches the manual test — not when the code merely looks right.
