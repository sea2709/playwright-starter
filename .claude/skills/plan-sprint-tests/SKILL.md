---
name: plan-sprint-tests
description: Use when planning Playwright tests for a sprint for Dang's CareerForce Drupal — turning sprint tickets into a small, prioritised test plan for the playwright-starter project, including which tests are anonymous read-only and which need an editor login.
---

# Plan tests for a sprint

A sprint test plan is a short list, not a rewrite of the whole site.
Its job is to answer, before the sprint ends: what did we change, what
must still work, and who checks each item?

## Inputs you need

- The sprint tickets (new features and bug fixes).
- The existing smoke list in `tests/smoke/` — currently home, search,
  listings, locations, tools, and login. These are the critical paths
  that must keep working.
- The rule for this project: tests run serially on a shared QA
  database, so the plan must stay small enough to stay green and fast.
  A plan nobody can run is worse than a short plan.

## Steps

1. **List the sprint's new features and fixes.** One line each, in
   plain language: what changed, for whom?
2. **For each item, write a test case first** (see the
   `write-test-case` skill): role, starting URL, steps, and expected
   visible result. If you cannot write the expected result in plain
   words, the ticket is not ready to test yet — ask the ticket owner.
3. **Add the critical paths that could break.** New work often breaks
   old pages. Add the smoke areas touched by the change (for example,
   a search change also needs the listings and search smoke tests).
   Do not add the whole site every sprint.
4. **Sort the list into two groups:**
   - **Anonymous, read-only** — viewing, searching, listing pages.
     These go in `tests/smoke/`, need no login, and are prod-safe.
   - **Authenticated / destructive** — creating, editing, deleting,
     admin pages. These go in `tests/`, need the editor session, and
     run on QA/staging only, never production.
5. **Keep the list small.** As a starting guide for a two-week sprint:
   one test per new feature, plus the critical paths touched by the
   change. If the list grows past what the team can run and verify in
   the sprint, cut the lowest-risk items and say so in the plan —
   do not silently drop them.
6. **Assign each item an owner for each step:**
   - Case author (QA/BA): writes the plain-language case, tests it
     manually first.
   - Test creator (Claude, with a developer reviewing code): turns the
     case into code with the `create-playwright-test` skill.
   - Verifier (QA): runs the test and confirms the trace with the
     `verify-playwright-test` skill.

## Plan template — copy this

```
Sprint: ______________
QA site: https://qa.careerforce.link

New features / fixes to test:
1. [title] — role: ______ — type: anonymous / authenticated — case author: ______
2. ...

Critical paths to re-check:
1. [home / search / listings / locations / tools / login] — why it could break: ______

Out of scope this sprint (and why):
- ...

Tests to run at sprint end:
- Anonymous smoke: npx playwright test --project=anonymous
- Full suite (QA only): npx playwright test
```

## At the end of the sprint

Run the plan's tests on the QA site, read the report, and record the
result against the plan: green, failed (site bug or test bug?), or
not run (and why). Carry failed items forward explicitly — do not let
a red test quietly disappear from the next sprint's plan. If the suite
stays green and fast, you may add one older critical flow per sprint;
if it starts flaking, fix stability before adding more tests.
