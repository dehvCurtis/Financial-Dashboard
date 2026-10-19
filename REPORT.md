# REPORT: verification of this bundle

Version 1.1.0, page stage 6o (VERSION). Reference run on a clean unzip of code-base.zip, Node 22, Chromium 1194 (Playwright build), Linux.

## Test totals

| Suite | Pass | Fail | What it checks |
|---|---|---|---|
| unit | 122 | 0 | every calc against hand worked fixture values |
| inv_run | 185 | 0 | 37 invariants on the data, the fixture and every dataset emptied one at a time |
| data_rules | 19 | 0 | store shape, sizes, titles, catalogue |
| static_scan | 9 | 0 | no HTML sinks, no inline handlers, no external URL, no dashes, no personal terms |
| backup_verify | 20 | 0 | verify_backup on good, broken and hostile input; safe output folders |
| docs_check | 12 | 0 | documents against code, templates load and run |
| inject | 4 | 0 | the page on data full of markup, script tags and javascript: URLs |
| browser | 137 | 0 | 10 pages at phone width, light and dark, folds, links, keyboard, backup kit |
| total | 508 | 0 | `bash runall.sh --full`, about 4 minutes (the browser suite runs in four parallel processes) |

`node tools/check_docs.js` passes every check on the generated documents. `node tools/deadsize.js` finds no unused function, constant or calc; the published files are under their size limits (page parts under 200 KB, selftest.js under 60 KB).

## Counts the documents state

37 datasets, 58 calcs, 28 correlation checks, 28 profile questions, 10 pages, 64 tiles, 82 cards, 25 charts, 13 tables. They are generated from the code (DATA_MODEL.md, CALCS.md, TILES.md, CORRELATIONS.md, QUESTIONS.md) and tools/check_docs.js fails when they drift.

## Privacy scan

Run over every text file in the repo, over the files inside code-base.zip, over the git history, and by OCR over the screenshots in demo_data/shots: names and institutions derived from the owner's real data (kept outside the repo), figures with four or more significant digits, digit runs of 6 or more, emails, phone numbers, URLs with credentials, secrets and tokens, and em or en dashes. No hit. The only external addresses are public benchmark source links in the reference datasets (age_benchmarks, ltv_benchmarks) and the npm registry in package-lock.json. One finding for the owner: the commit author identity of the first commit (SECURITY_REVIEW.md, F-11). The scan terms themselves are not stored in the repo.

## Docs against code

Every dataset id in the built page has a template, a demo file and a DATA_MODEL.md entry; every calc has a CALCS.md entry; every rendered tile has a TILES.md row and none is listed that does not render; every check id and profile question id matches. Designs for features that are not in the shipped page (target mix, picks, rollover and transfers, the researched-ticker archive, the bank transaction table) are marked as not built in ARCHITECTURE.md section 5.5.

## File tree

Root: the 32 documents listed in INDEX.md, VERSION, MANIFEST.json, CHECKSUMS.sha256, .gitignore, code-base.zip, templates/ (37 starter files, planned/, offpage/ with 3, README.md), demo_data/ (37 files, offpage/ with 3, shots/ with 11 PNG). `sha256sum -c CHECKSUMS.sha256` verifies every file.

## Reviews

SECURITY_REVIEW.md (independent security review) and COMPLETENESS_REVIEW.md (new-user walk through) list their findings and fixes.
