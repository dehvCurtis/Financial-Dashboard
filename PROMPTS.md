# PROMPTS: paste-ready prompts

Replace nothing in angle brackets except where it says so. All prompts assume the bundle is attached (at least README.md, CLAUDE.md, ARCHITECTURE.md, STYLE-GUIDE.md, DATA_MODEL.md, templates/). They contain no personal data and neither should your answers to the interview if you share the chat; give figures only to your own Claude session.

## 1. First build

```
Build and maintain a private personal finance dashboard for me as a Claude Artifact of the Dashboard type. I attached a bundle that describes structure only. It has no one's real numbers, lenders, employer or place. Never copy or assume a figure from it; ask me for my own.

Do these in order:
1. Read README.md, CLAUDE.md, ARCHITECTURE.md and STYLE-GUIDE.md completely. Do not build yet.
2. Tell me, in five lines, what you will build and what you need from me.
3. Run the Stage 0 probe (ARCHITECTURE.md section 17) and record the contract version and which capabilities my account offers.
4. Interview me from QUESTIONS.md and INTAKE_GUIDE.md, a few questions at a time. First the profile questions: do I have a mortgage, credit cards, and so on. Never assume a category exists.
5. Accept screenshots, PDFs, CSV exports and pasted text. Read every attachment fully, read the numbers back before relying on them, and save with source and as-of date. Keep only extracted numbers, never account numbers or originals.
6. Build the stages in ARCHITECTURE.md section 17 one at a time, each passing the tests before the next. Start from templates/.
7. Do not publish, share or delete anything until I say so. Show me what will change first.
8. At the end of every session tell me what is still needed and what to send next.

Rules: never invent a number (show "Needs: X" and make a task), no em dashes or en dashes anywhere, plain words, tell me if there is a better way than what I ask.
```

## 2. Add a statement

```
Here is my <pay stub | card statement | bank statement | mortgage statement | 401(k) statement>. Follow INTAKE_GUIDE.md: read every page, read back the key numbers, save the rows with source and as-of date, run the checks that apply, close the task that asked for it, and tell me in one or two sentences what changed. Do not publish yet.
```

## 3. Update an existing dashboard to this version

```
Upgrade my existing dashboard to the version in the attached bundle (VERSION, CHANGELOG.md). My dashboard link is <link>.
1. Read the bundle and my current dashboard (page source, datasets, dataset documents). Change nothing.
2. List the differences in plain words: datasets, fields, renamed fields, pages and cards, calcs, checks. Mark each missing, different or fine. Wait for my go.
3. Back up first: every dataset as JSON, the page source, and tell me where they are. Verify with tools/verify_backup.js. Change nothing until the backup exists.
4. Freeze a copy of my data and run the calcs for a regression baseline. Dollar values must match to the cent after the upgrade.
5. Upgrade as a facelift, not a rebuild. Keep every chart, table and check I have; move, do not delete.
6. Ask me for every Needs item. Never invent a number.
7. Test, then show me the summary. Publish only when I say so.
```

## 4. Monthly check-in

```
It is the monthly check-in. Follow RUNBOOK.md: ask me for this month's statements, update the balances, run the checks, refresh the age statistics and limits if due, add a scores_history row (and a metric_snapshots row if that dataset exists), and make the backup (BACKUP_FORMAT.md, BACKUP_GOOGLE_DRIVE.md). Tell me the three most useful next moves.
```

## 5. Backup to Google Drive

```
Follow BACKUP_GOOGLE_DRIVE.md. I will attach the backup JSON and the rebuild kit. Verify them, create the dated folder in my Money Ops Backups folder, upload, confirm names and sizes, and tell me what you saved and where.
```

## 6. Restore

```
Rebuild my Money Operations Center from the attached backup kit. Follow RESTORE.md and BACKUP_FORMAT.md section 8. Verify the kit first. Create a new dashboard, do not touch any existing one, and compare the check values after the restore.
```

## 7. Change the code base

```
Work in the code base (src/). Read CLAUDE.md. Make this change: <describe>. Keep tests green: run the browser-free suites while working and the full suite at the end. Regenerate and check the docs. Do not publish.
```

## 8. Review before sharing the bundle

```
Run the privacy scan from SECURITY_AND_PRIVACY.md over the bundle: my names, institutions, amounts of 100 or more, identifiers, digit runs of 6 or more, and em or en dashes. Report every hit with file and line. Fix or justify each. Do not share anything.
```
