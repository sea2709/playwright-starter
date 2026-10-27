---
name: write-test-case
description: Use when a QA, BA, or other non-developer wants to write a test case in plain language for Dang's CareerForce Drupal, before any Playwright code is written. No code, Git, or Playwright knowledge is needed.
---

# Write a test case (plain language, no code)

A test case is a short description of one user journey, written so that
someone else — a teammate or Claude — can turn it into an automated test
without guessing. Write the case first, test it manually first, and only
then ask for code. Code follows the case, not the other way around.

## Template — copy this and fill it in

```
Test title: ________________________________________
User role: anonymous / editor / admin (pick one)
Starting URL: ______________________________________
  (for example: / , /search, /node/add/article, /admin/content)

GIVEN: ___________________________________________
  (what is already true before the test starts)

WHEN:  ___________________________________________
  (what the user does, step by step, in plain words)

THEN (what the user should SEE): __________________
  (visible result on the page — a heading, a message, a listing)

Test data needed: __________________________________
  (use a unique title each run, for example "Playwright test <date/time>")

Cleanup: delete the test content afterwards / nothing to clean up
```

## Rules

- **One journey per case.** If the case says "and then also...", split it
  into two cases.
- **Say what the user sees, not what the system does.** Write "the article
  title and body are visible on the page", not "the node is saved in the
  database" or "the API returns 200".
- **Name the role.** Anonymous visitors, editors, and admins see different
  pages on a Drupal site. A case without a role is incomplete.
- **Be specific about the starting point.** A URL or a named page, not
  "go to the site and find it".
- **Use unique test data.** If the test creates content, give it a unique
  title each run so two runs do not collide on a shared QA database.
- **Say whether cleanup is needed.** Created content must be deleted
  afterwards. Read-only checks (viewing, searching) need no cleanup.

## Worked examples

```
Test title: Published test article is visible
User role: editor (to create it), then anonymous (to view it)
Starting URL: /node/<nid of the created article>
GIVEN: an article was created and published
WHEN:  a visitor opens the article page
THEN:  the article title and body are visible on the page
Cleanup: delete the test article
```

```
Test title: Anonymous visitor can search listings
User role: anonymous
Starting URL: /search
GIVEN: the site is up and listings exist
WHEN:  the visitor searches for a known listing
THEN:  that listing appears in the search results
Cleanup: nothing to clean up (read-only)
```

## What not to include

- Selectors, CSS classes, XPath, or element IDs copied from the page.
- Database checks, API status codes, or internal Drupal terms, unless a
  developer specifically asks for them.
- Passwords, secrets, or real personal data.
- Several journeys, edge cases, and error cases in one case. Write the
  happy path first. Edge cases become their own cases later.

## Hand-off

When the case is written, test the journey manually once yourself. If the
manual test fails, that is a site issue, not a test issue — report it as
such. If the manual test passes, hand the case to the
`create-playwright-test` skill: it turns this case into code. You validate
the finished test by watching its trace/video and confirming it matches
what you tested manually — not by reading code.
