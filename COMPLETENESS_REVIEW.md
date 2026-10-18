# COMPLETENESS_REVIEW: walking the repo as a brand new user

Method: a reviewer with only this repo and Claude followed README.md from the top: read the files in order, unzipped code-base.zip on a clean folder, ran `npm install`, built the page, ran the tests and the docs check, and then compared every claim in the documents with the code. Result before the fixes: 5 pass, 4 partial, 11 fail. After the fixes below: 20 of 20 pass, with open decisions for the owner listed under Gaps (the license and D9 first).

## Checklist

| # | Item | Before | After | Notes and fix |
|---|---|---|---|---|
| 1 | README quick start runs end to end on a clean machine | Pass | Pass | unzip, `npm install` (52 packages, a few seconds), build, `bash runall.sh --full`: 471 checks before the fixes, 481 after, about 7.5 to 8 minutes |
| 2 | Every file README.md names exists | Fail | Pass | REPORT.md was named three times and missing: created |
| 3 | Every link in INDEX.md and README.md resolves | Fail | Pass | same file; INDEX now lists AGENT_PROMPT, SECURITY, the reviews and .gitignore |
| 4 | Every dataset id in the built page has a template, a demo file and a DATA_MODEL entry | Pass | Pass | 39 of 39. The unbuilt source list also held three dead ids (candidates, roth_plan, transfers); replaced with the generated list |
| 5 | Every calc has a CALCS entry, every tile a TILES row, every question id a QUESTIONS entry | Pass | Pass | tools/check_docs.js: 18 checks pass; 58 calcs, 184 tile rows, 28 questions, 27 checks |
| 6 | PUBLISH.md steps are in a runnable order | Partial | Pass | order was right; added the yes needed before each step that creates something, and the dead code and size step before publish |
| 7 | PROMPTS.md covers build, interview, pay stub, bank, card, bill, loan or mortgage, 401(k) or brokerage, tax form, monthly review, backup, restore, annual refresh, fix a wrong number, change country or currency | Fail | Pass | had 8 prompts covering 5 of the 15 jobs; rewritten with 18 prompts and a shared safety footer |
| 8 | Nothing points to a file that only exists inside a kit | Fail | Pass | PROMPTS.md asked Claude to "follow RESTORE.md" (a file inside every kit, not in the repo): now BACKUP_FORMAT.md section 8 |
| 9 | The Google Drive backup procedure is complete (what, when, naming, verify, restore) | Partial | Pass | facts were spread over the file; added one table; Drive stays optional; confirmation before upload |
| 10 | Tax form intake exists | Fail | Pass | INTAKE_GUIDE.md had a year end mortgage form and refunds only; added a W-2, 1099 and return row |
| 11 | Versions and prerequisites are stated | Partial | Pass | Node 22 tested (20 or later asked in package.json), npm 10, bash, unzip, Chromium install command, package versions match the lockfile |
| 12 | License TODO is flagged | Pass | Pass | README.md, OPEN_ITEMS.md D1, package.json `UNLICENSED`. The owner must choose one |
| 13 | Nothing refers to files, paths, chats or people outside the repo | Fail | Pass | comments named a private tools folder, the live runtime source path and a staged data folder (build.js, tests/inv_run.js, runall.sh); dataset descriptions said "stated by the owner" and "credit union", "Truck", "Ten semimonthly stubs, May to Oct 2026": made generic |
| 14 | Removed features are not still described | Fail | Pass | ARCHITECTURE.md 5.5, the More sheet row, the Investments tile lists and the Home retirement card still described target mix, picks, rollover, transfers, old plan datasets and tile ids that the page does not render: marked not built or removed, ids corrected against TILES.md |
| 15 | Counts in the documents match the code | Pass | Pass | 10 pages, 39 datasets, 58 calcs, 27 checks, 28 questions, 64 tiles, 82 cards, 25 charts, 13 tables, 34 invariants, 11 screenshots, 39 templates (all generated or re-counted); VERSION documents 28 became 32 |
| 16 | Test run time is stated correctly | Fail | Pass | "about seven minutes" became about eight (466 s and 445 s measured) |
| 17 | Dead code and size pass is part of the documented loop | Fail | Pass | tools/deadsize.js existed but no document or script ran it: CLAUDE.md loop step 5 and PUBLISH.md. Result: nothing to remove |
| 18 | A first message for a new user exists | Fail | Pass | AGENT_PROMPT.md and README.md "Start here" |
| 19 | Stray files | Fail | Pass | .gen_ok removed from the root and the manifest |
| 20 | The suite passes on a clean machine | Fail (flaky) | Pass | two of three full runs failed in docs_check because the tile inventory missed lazily drawn cards (see "Flaky check"); fixed in tools/tiles_inventory.js |

## Gaps that remain (owner decisions)

1. License (OPEN_ITEMS.md D1). Until chosen no license is granted.
2. Net worth rows and copy say "House" and "Truck" (D9). The labels come from page code; changing them touches approved inventories and tests, so it was not done in a documentation pass. A user with neither will still see the words.
3. Contact address in SECURITY.md (D7) and the first commit's author identity (D8).
4. The page and benchmarks are US style (LOCALIZATION.md). Prompt 15 walks through a change but a country other than the US needs new reference data.
5. A real phone check of the bottom bar and a re-run of the Stage 0 probe on each account (OPEN_ITEMS.md).

## Flaky check (fixed)

Two of three full runs on a clean unzip failed in docs_check with "tiles_notes.json has no entry for a tile that is gone", naming a different set of cards each time (the self-test and backup cards, and three Pay tiles). Cause: tools/tiles_inventory.js waited a fixed 200 ms after opening each page, but some cards are drawn lazily (viewport observer, lazily loaded self-test file), and on a busy or small machine they were not there yet. The stale-note check then reported tiles as gone. Fix: the inventory now waits until the page has stopped changing (four equal readings, 150 ms apart, at most 12 s per page). Check: three inventory runs with six CPU spinners on a two core machine produced byte identical output to a quiet run, and the full suite passes. Before the fix the shipped bundle could fail its own test on a slow machine.

## Fixes applied

AGENT_PROMPT.md, README.md "Start here" and prerequisites, CLAUDE.md, INDEX.md, PROMPTS.md (18 prompts), INTAKE_GUIDE.md (tax form, untrusted content), RUNBOOK.md (fix a wrong number, version control), BACKUP_GOOGLE_DRIVE.md (table), ENVIRONMENT.md, PUBLISH.md, OPEN_ITEMS.md, ARCHITECTURE.md (5.5 status, More sheet, Investments and Retirement tile lists), REPORT.md (new), dataset descriptions and comments in the code base, package.json (`private`, `engines`), tools/tiles_inventory.js (settle wait), CHANGELOG.md, removed .gen_ok. MANIFEST.json, CHECKSUMS.sha256 and code-base.zip were rebuilt after every change and `sha256sum -c CHECKSUMS.sha256` passes.
