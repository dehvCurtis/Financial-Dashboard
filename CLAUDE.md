# CLAUDE.md: working rules for the agent

Read this first in every session. It is short on purpose; the reasons are in ARCHITECTURE.md (section numbers are cited as ARCH n).

## Hard rules

1. Never invent a number. A missing input shows "Needs: X", becomes a task, and you ask the user in plain words (ARCH 3).
2. Store each number once, as its latest confirmed value, with `source` and `as_of_date`. A correction replaces the old value in place. Only history datasets keep dated rows (ARCH 5).
3. Never store account numbers, SSNs, routing numbers, loan ids, tax ids or street addresses. Store extracted fields only. The user keeps the original files (SECURITY_AND_PRIVACY.md).
4. No em dashes and no en dashes anywhere: text, code comments, data, documents. No exclamation marks in dashboard copy.
5. Do not publish, share or delete anything unless the user asks in that turn. A first publish is private. Ask before the first publish of an update.
6. Do not call Artifact or ArtifactData in a test or a dry run. The tests run in the browser-free engine and a local Chromium only.
7. One chart or table per fact. Do not remove a gamified element, chart or graph the user approved; moving is fine; count before and after (ARCH 19).
8. No combined score, no streaks, no points for typing a number (ARCH 12).
9. Tell the user about a better way when there is one, and double check their own suggestions before building them.

## Where things are

| Need | File |
|---|---|
| What each dataset holds, fields, units | DATA_MODEL.md |
| What each calc does and reads | CALCS.md |
| Every tile and where it links | TILES.md |
| What to ask and in which words | QUESTIONS.md, INTAKE_GUIDE.md |
| Runtime facts that broke builds | RUNTIME_CONTRACT.md |
| How to publish | PUBLISH.md |
| Monthly and weekly routine | RUNBOOK.md, SCHEDULED_TASKS.md |
| Backup and restore | BACKUP_FORMAT.md, BACKUP_GOOGLE_DRIVE.md |

## Working loop for a change

1. Read the dataset or calc entry in DATA_MODEL.md or CALCS.md. Check the code in `src/page/js` for the same name.
2. Change the data (templates or the user's datasets) or the code. Keep datasets flat: nested cells only on the allow-list in ARCH 5.4.
3. Build: `node tools/build_page.js && node tools/build_split.js`.
4. Test: `bash runall.sh --full` (about seven minutes with the browser suite). Faster while working: `node tests/unit.js`, `node tests/inv_run.js`, `node tests/data_rules.js`, `node tests/static_scan.js`, `node tests/backup_verify.js`.
5. Docs: `node tools/tiles_inventory.js && node tools/tiles_inventory.js --blank && node tools/docs_facts.js && node tools/gen_docs.js`, then `node tools/check_docs.js`. A render that does not match TILES.md fails the check.
6. Only after all green and only if the user asked: publish (PUBLISH.md).

## Runtime facts you will trip over

Nested objects reach the page as JSON text. An empty array does not load. A calc that throws takes down every calc that lists it as an input. A missing key is null, never undefined. Titles are 30 characters at most. Each document is 256 KB at most. Details: RUNTIME_CONTRACT.md.

## Style of replies to the user

Plain words. One topic per turn during the interview. Read numbers back before relying on them. After each batch of changes say in one or two sentences what changed and what is still needed.
