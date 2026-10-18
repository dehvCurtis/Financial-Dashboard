# PUBLISH: getting the dashboard live

Nothing is published unless the user asks in that turn. A first publish is private (only the owner can open it). Sharing is a separate decision.

## Kinds of publish

| Kind | When | What changes |
|---|---|---|
| Data only | a statement was added, a number corrected | one or more dataset assets and their dataset documents |
| Page code | a calc, card or layout changed | page parts (index.html, app_a.js, app_b.js, app_c.js, selftest.js) |
| Both | a feature that needs a new dataset | datasets first, then documents, then page parts |

## Before any publish

1. `bash runall.sh --full` is green. `tools/publish.js` refuses to run unless the record of the last full run matches the files as they are now.
2. `node tools/deadsize.js` found nothing to remove and no file over its size limit, and `node tools/check_docs.js` is green when tiles or datasets changed.
3. The user has seen a plain summary of what will change and has said go.
4. The privacy scan of the files to publish is clean: no dashes, no account numbers, no emails, no digit runs of 8 or more outside ISO dates.

## Steps

1. `node tools/publish.js --stage <n> [--pins pins.json]` builds the files, checks sizes (page parts under 200 KB, documents under 256 KB, titles under 31 characters), loads every dataset through the runtime parser, runs every calc, checks for dashes, and writes `out/stage_<n>/PUBLISH_LIST.md` plus one payload per file. It never writes to the store.
2. Read the live version of each document you are about to change (the pin). A file with no pin is a create.
3. Upload changed datasets as assets, one per publish call, using the exact url each result returns.
4. Write the changed dataset documents in one batch pinned to the versions read: title (30 characters at most), description, `source: {kind: "file", url, name: "<id>.json", format: "json"}`, `updated`.
5. Publish the page parts (templates, draw, calc, helpers).
6. Publish the shell (index.html) last.
7. Open the live page. Read the debug panel (triple tap on the title): every dataset ok with its row count, every calc ran, no console errors.
8. Run the monitor checks: no NaN or undefined text, every Needs line matches a known need, no personal identifiers, static scan clean. Report failures to the user at once.
9. Keep PUBLISH_LIST.md with the release: file, bytes, sha256, pin, new description.

## First build on a new account

Each step that creates something in the user's account (the artifact in step 2, the probe page in step 3, the uploads in step 4) needs the user's yes first. All of them are private.

1. Call the Artifact tool's quickstart with intent "other" and read the Dashboard type's instructions.
2. Create the artifact from the type with a title and no files.
3. Stage 0 probe (ARCHITECTURE.md section 17): publish a tiny test page; record the contract version and whether db, user and downloads resolve; confirm or correct RUNTIME_CONTRACT.md.
4. Upload the starter datasets from templates/ (or demo_data/ for a trial) one per call, write the documents, publish the page, then the shell.
5. Fill through the interview and statements (INTAKE_GUIDE.md).

## After the publish

Tell the user what the page is, or what changed, in one sentence. Do not paste links unless asked. Offer to pin it. Update CHANGELOG.md with the stage and the date.

## Rollback

The store keeps earlier versions of each document. To roll back, read the previous version of the affected files and publish it as the new version, then verify as in step 7. Data rollback is the same, or restore from a backup (BACKUP_FORMAT.md section 8).

## Never

Publish a page that imitates a real organization or person. Publish a page containing real account numbers, SSNs or credentials. Use force overwrite unless the user named that exact version to discard. Delete an artifact unless the user asked in that turn.
