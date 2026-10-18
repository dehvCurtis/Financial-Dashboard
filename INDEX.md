# INDEX: every file in the bundle

Integrity: MANIFEST.json lists every file with its size and sha256; CHECKSUMS.sha256 is the same in `sha256sum -c` form. Version: VERSION.

## Documents

| File | Purpose |
|---|---|
| README.md | what the bundle is and how to start |
| AGENT_PROMPT.md | the short context prompt every Claude reads first, with the first message for a new user |
| CLAUDE.md | the working rules for the agent |
| ARCHITECTURE.md | full build guide, sections 0 to 20, refreshed to page stage 6i |
| STYLE-GUIDE.md | tokens, layout, the measured bottom bar shell, accessibility |
| ENVIRONMENT.md | machine, packages, browser, test suites, variables |
| RUNTIME_CONTRACT.md | the Artifact runtime facts that broke builds |
| DATA_MODEL.md | every dataset: class, key, fields, units (generated) |
| CALCS.md | every calc: inputs, rule, output (generated) |
| TILES.md | every card, tile, chart and table with formula, links and fold (generated, checked against the render) |
| CORRELATIONS.md | the checks that tie numbers together; PLANNED metric_snapshots |
| SCORING.md | Financial and Risk scores |
| SOURCES_AND_BENCHMARKS.md | reference data, source rules, refresh |
| QUESTIONS.md | profile questions (generated from the code) and the interview |
| INTAKE_GUIDE.md | from a statement to dataset rows, with invented examples |
| PROMPTS.md | paste-ready prompts |
| PUBLISH.md | publishing, monitoring, rollback |
| RUNBOOK.md | pay day, weekly, monthly, yearly routines and troubleshooting |
| SCHEDULED_TASKS.md | reminders and routines |
| BACKUP_FORMAT.md | schema mo-backup-2, kit, manifest, verify, restore |
| BACKUP_GOOGLE_DRIVE.md | the Google Drive backup procedure |
| SECURITY.md | how to report a problem, scope, the threat model |
| SECURITY_AND_PRIVACY.md | what is never stored, sharing rules, the privacy scan |
| SECURITY_REVIEW.md | the independent security review: findings, fixes, what could not be checked |
| LOCALIZATION.md | using it outside the United States |
| GLOSSARY.md | terms |
| DEMO_DATA.md | the invented dataset and screenshots |
| CHANGELOG.md | feature versions and stage summary |
| OPEN_ITEMS.md | decisions needed, what is not built, known gaps |
| REPORT.md | verification: privacy scan, docs against code, file tree, test totals |
| INDEX.md | this file |

## Data and code

| Path | Content |
|---|---|
| templates/ | one starter file per dataset (one all-null row, or shipped reference content), planned/metric_snapshots.json, README.md |
| demo_data/ | the invented dataset, one JSON per dataset, and shots/ with one PNG per page |
| code-base.zip | src/: page, tools, tests with synthetic fixtures, docs_src (the sources of the generated documents) |
| MANIFEST.json | file list with sizes and sha256, bundle metadata |
| CHECKSUMS.sha256 | the same hashes for `sha256sum -c` |
| VERSION | bundle, schema, stage and contract versions |
| .gitignore | keeps backups, statements, secrets, node_modules and build output out of version control |

## Which document answers which question

| Question | Document |
|---|---|
| Is it safe, and how do I report a problem? | SECURITY.md, SECURITY_REVIEW.md |
| How do I start? | README.md (Start here), AGENT_PROMPT.md, PROMPTS.md |
| What does this tile mean? | TILES.md |
| What does this dataset hold? | DATA_MODEL.md, templates/ |
| Why does a card say Needs? | ARCHITECTURE.md section 3, QUESTIONS.md |
| How do I add a statement? | INTAKE_GUIDE.md |
| How do I back up and restore? | BACKUP_FORMAT.md, BACKUP_GOOGLE_DRIVE.md |
| How do I publish? | PUBLISH.md |
| What broke before? | RUNTIME_CONTRACT.md |
| What is not done? | OPEN_ITEMS.md |
