# ENVIRONMENT: what you need to build, test and run

## Two ways to use the bundle

| Way | Needs | Gives |
|---|---|---|
| Chat only | A Claude chat with the Artifact tool and file attachments | Claude builds the dashboard from the documents and templates, fills it from your answers and statements. No code is run on your machine |
| Code base | A machine with Node.js and a Chromium browser, plus Claude Code (or any agent that runs shell commands) | build, the full test suite, the docs check, the backup verify tool, screenshots |

Chat only is enough to use the dashboard. The code base is for changing it with confidence.

## Code base requirements

| Item | Version used for the reference run | Notes |
|---|---|---|
| Node.js | 22.x | the build, the tests and every tool are plain Node scripts |
| npm | 10.x | `npm install` in src/ |
| Packages | acorn 8.19.0, esbuild 0.28.2, playwright-core 1.64.1, terser 5.51.2, d3 7.9.0 | from package.json; d3 is bundled into the page at build time |
| Chromium | any recent build | only the browser suite and the screenshots need it. Set `CHROMIUM_PATH` to the binary, or put a Playwright browser under `PLAYWRIGHT_BROWSERS_PATH` or `/opt/pw-browsers` |
| Python 3 | optional | only helper scripts outside the code base use it |
| OS | Linux or macOS | tests/lib/web.js looks for Chromium in the usual places |

No network is needed after `npm install`. The tests never contact the Artifact service or any website.

## Setup from the zip

```
unzip code-base.zip -d money-ops && cd money-ops/src
npm install
export CHROMIUM_PATH=/path/to/chromium      # only for the browser suite
node tools/build_page.js && node tools/build_split.js
bash runall.sh --full
```

`bash runall.sh` (without --full) runs only what the last change affects (tools/affected.js) and is faster while working.

## Test suites

| Suite | File | What it covers | Needs a browser |
|---|---|---|---|
| unit | tests/unit.js | one or more fixture tests per calc, hand worked values | no |
| inv_run | tests/inv_run.js | 34 page invariants on the data, the fixture and every dataset emptied one at a time | no |
| data_rules | tests/data_rules.js | store shape: loads under the runtime parser, sizes, titles, catalogue | no |
| static_scan | tests/static_scan.js | no dashes, no personal terms, no innerHTML, eval or inline handlers | no |
| backup_verify | tests/backup_verify.js | tools/verify_backup.js against good and broken backups | no |
| browser | tests/browser.js | the 10 pages at phone width, light and dark, folds, links, keyboard, backup kit built by the real page code | yes |

The data folder `data_all/` holds the invented example dataset (also in demo_data/). Fixtures for the unit tests are in tests/fixtures/base (invented).

## Where the example data sits

`src/data_all/` is the dataset the build embeds for the tests and the screenshots. Replace it with your own only on a private copy; the tests that scan for personal terms read it at run time, so your own names in it are caught by the page code scan.

## Environment variables

| Variable | Use |
|---|---|
| CHROMIUM_PATH | path to the Chromium binary |
| PLAYWRIGHT_BROWSERS_PATH | folder holding a Playwright browser |
| DATA_EXTRA | colon separated extra data folders for the data independent suites |
| APPROVE_MARKS=1 | regenerate the approved element inventories in tests/lib after an intended change (review the diff) |
| ONLY=calc,... | run a subset of unit tests |
| STRICT=1 | makes build.js emulate the live store strictly (set by the test library for browser pages) |

## Claude Artifact side

Needs an account that offers the Dashboard Artifact type, with the capabilities db, user and downloads. The Stage 0 probe in ARCHITECTURE.md section 17 records what your account actually offers. The contract version the reference build was observed on is 0.2.67 (RUNTIME_CONTRACT.md).

## Time

A full run with the browser suite takes about seven minutes on a small machine; everything except the browser suite takes under one minute.
