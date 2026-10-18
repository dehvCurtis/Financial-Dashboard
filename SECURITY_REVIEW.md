# SECURITY_REVIEW: independent review of the whole repo

Scope: every file in the repo, every file inside code-base.zip (unzipped into a fresh folder and read with isolated interpreters where practical; no repo script that reaches the network was run), the git history, and the screenshots. Reviewer: an independent Claude session. Line numbers are those of the files before the fixes below.

## Result in one line

0 High, 4 Medium, 7 Low, 6 Info. Every Medium and every Low is fixed or documented. Nothing real (name, figure, institution, identifier, secret, URL with credentials, artifact id) was found in the files, the zip, the history or the screenshots.

## Findings

| # | Sev | Where | Evidence | Fix | Status |
|---|---|---|---|---|---|
| F-1 | Medium | BACKUP_FORMAT.md:107 (restore step 8), ARCHITECTURE.md section 18 step 8, page/js/90_wiring.js:149 (RESTORE text in every kit) | The restore told Claude to publish the `page/` files from the backup kit. A kit is an untrusted file; altered page code would run with the user's data. | Restore builds the page from the repo's code base; the kit's `page/` is for comparison and is shown as a diff before any use; RESTORE.md text in a kit is data, the steps in this repo are the instructions. Docs and the RESTORE text in the page were updated. | Fixed |
| F-2 | Medium | repo root (no .gitignore) | `git ls-files` shows no ignore rules; a downloaded `dashboard-backup-*.json`, a kit zip, a statement PDF or CSV, or a `.env` in the folder could be committed and pushed. | Added .gitignore at the root and in src/ (backups, kits, statements, spreadsheets, .env and keys, node_modules, work, out, docs_out, logs). README and SECURITY_AND_PRIVACY.md warn that a backup holds real figures. | Fixed |
| F-3 | Medium | tools/backup_lib.js:16 (readZip), used by tools/verify_backup.js | No limit on entries or sizes: `zlib.inflateRawSync` of a small deflate bomb allocates without bound; offsets from the file were used without bounds checks; entry names were stored as keys of a plain object (`__proto__`); names with `..` or absolute paths were accepted. | Limits (2000 entries, 64 MB per file, 256 MB total), `maxOutputLength`, bounds checks, `Object.create(null)`, unsafe names refused. Tests: deflate bomb, slip names, truncated and garbled zips. | Fixed |
| F-4 | Medium | SCHEDULED_TASKS.md:16 | Told Claude to suggest switching scheduled tasks to automatic approval. A scheduled run has no user present and may touch Drive or mail. | Removed; added "What a scheduled run may do": remind, prepare and ask only; publishing, uploads, email and deletes need a live yes. | Fixed |
| F-5 | Low | tools/verify_backup.js:9 | 8 of 15 hand-made hostile inputs (JSON `null`, a number, a string, wrong types in the manifest, null rows) threw a TypeError with a stack trace instead of a failed check. It failed closed by exit code only. | `verify` wraps the checks: any unexpected structure is a failed check; size limit before reading. Tests added. | Fixed |
| F-6 | Low | build.js:24 (test harness) | `window.__TEXTS=${JSON.stringify(texts)}` inside an inline script. A data value holding a closing script tag ended the block and ran markup in the harness page. The real artifact does not embed data this way, so production is not affected. | `<` is written as `<`. This made the injection test possible. | Fixed |
| F-7 | Low | RESTORE.md inside the kit; PROMPTS.md prompt 6 | An instruction file that travels inside an untrusted kit. | Same as F-1: wording says it is data; prompt 12 restates the rule. | Fixed |
| F-8 | Low | PUBLISH.md:32 to 38 | The first build created an artifact and published a probe page without saying a yes was needed. | Each creating step needs a yes; all are private. | Fixed |
| F-9 | Low | tools/make_templates.js:10, tools/merge_data.js:14 | `fs.rmSync(out,{recursive:true,force:true})` on a user supplied path; `--out /` or `--out ~` would delete it. | tools/safe_out.js refuses a root, home, working, code base or parent folder. Test added. | Fixed |
| F-10 | Low | tests/browser.js:92 to 94 | `execSync('unzip -p '+zp+' "'+f.path+'"')`: shell string built from a manifest value. Inputs are the page's own output in tests, but the pattern is unsafe. | `execFileSync('unzip',[...])`. | Fixed |
| F-11 | Low | tools/backup_lib.js:11 and page/js/90_wiring.js:183 (csvCell) | A text cell that starts with `=`, `+`, `@` is written to the CSV view as is; a spreadsheet may run it as a formula. Values come from uploaded statements. | Not changed in code: escaping would change the bytes the manifest hashes cover and the CSV is documented as a view only. BACKUP_FORMAT.md and SECURITY.md say to open CSVs as text and never restore from them. The JSON is the source. | Documented |
| F-12 | Info | git commit 376cfc2 | The first commit's author field holds a personal email address. It becomes public with the repository. | Owner decision (OPEN_ITEMS.md D8): rewrite the author or accept. Not changed by this review. | Open |
| F-13 | Info | .gen_ok | A build marker (timestamp) copied into the root. | Removed. | Fixed |
| F-14 | Info | tests/static_scan.js, tests/lib/owner_terms.js | The personal-term scan derives its terms from `data_all`. In this repo that is the invented data, so the scan proves the structure is clean but cannot catch the owner's real names. | The owner's terms were scanned for this review (REPORT.md). Repeat the scan with the private list before each push. | Open (process) |
| F-15 | Info | package.json, package-lock.json | `npm audit --omit=dev`: 0 vulnerabilities. 77 locked packages, all from registry.npmjs.org with integrity hashes. Only esbuild has an install script (its platform binary). No postinstall script in this package. Added `private: true` and `engines`. | none needed | Info |
| F-16 | Info | demo_data and templates age_metrics_config.json:165, :221 | Config rows hold presentation sentences ("Present as a sentence ..."). They look like instructions. | They are data. Rule 8 in AGENT_PROMPT.md and CLAUDE.md covers it. No injected instruction found in any data file. | Info |
| F-17 | Info | page/js | Positive: no innerHTML, outerHTML, insertAdjacentHTML, document.write, eval, new Function, inline handler, anchor, iframe, external script, fetch, XHR, postMessage, window.open or cookie anywhere. Only `window.claude.use` capabilities, and localStorage (`moc.coll.*`, `mo_last_backup`) inside try/catch. `setAttribute` is called only with fixed names and computed numbers. | none needed | Info |

## Injection test of the real page

tests/inject.js (added) appends `<img src=x onerror=...><script>...</script> javascript:...` to 2,082 text values (191 distinct strings, applied consistently so cross references still match) of the invented dataset, builds the page on it, opens all 10 pages with every fold open and checks: no script ran, no img, extra script, anchor, frame, object, form or base element appeared, no element has an `on*` attribute, the payload is visible as plain text, and no page error. It passes. The test also runs the harness build that F-6 fixed.

## Tooling

No `curl | sh`, no `eval`, no downloaded code. `child_process` is used with argument arrays (`execFileSync`) in tools and tests after F-10. Writes go to work/, out/, docs_out/ or a folder the user names (guarded by F-9). tools/verify_backup.js never extracts files to disk, so zip slip cannot happen there; the restore is done by Claude from verified bytes. publish.js builds payloads and never writes to the store.

## Agent safety

CLAUDE.md, AGENT_PROMPT.md, PROMPTS.md, INTAKE_GUIDE.md, RUNBOOK.md, SCHEDULED_TASKS.md, PUBLISH.md, BACKUP_GOOGLE_DRIVE.md and BACKUP_FORMAT.md were read for any instruction to run, publish, upload, email, delete, widen permissions or skip a safeguard. After F-1, F-4 and F-8 each such action requires an explicit yes in the live turn, uploaded content is data, mail and Drive are optional and scoped to what the user asked, and nothing tells Claude to disable a safeguard. Force overwrite and deleting an artifact stay forbidden unless the user names them.

## Data handling

Stored: amounts, rates, dates, balances, statement line items, the user's own labels, tasks, answers. Never stored: SSN or tax id, full account numbers, routing numbers, loan ids, card numbers, street addresses, original files (export strips fields named like them; verify_backup flags them). The dashboard is private by default; sharing a link exposes every number. Backups hold real data: .gitignore, README and SECURITY_AND_PRIVACY.md say so. SECURITY.md has the reporting path and the threat model.

## What I checked

Secrets, tokens, keys, emails, phones, URLs, account-like digit runs and personal terms in the working tree, the unzipped code base and git history; OCR of all 11 screenshots; page code for HTML sinks, dynamic code, network, storage and attribute use; a browser injection run; tooling for command injection, path traversal, deletes, curl-pipe patterns and install scripts; zip reading and the verify tool against 322 malformed or hostile inputs; npm audit and lockfile integrity; all agent-facing documents.

## What I could not check

The Artifact service itself (how the live store, sharing and the downloads capability enforce access); how a live Claude session reacts to a hostile statement (the rules are written, not exercised end to end); the Google Drive and mail connectors; Windows and macOS runs; Firefox and Safari; Claude's PDF reading of real statements; that the platform's own secret scanning is switched on for the repository; `npm audit signatures`; whether the dependency tarballs match their source repositories.
