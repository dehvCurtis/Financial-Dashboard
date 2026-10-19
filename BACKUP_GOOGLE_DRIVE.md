# BACKUP_GOOGLE_DRIVE: keeping the monthly backup in your own Google Drive

The page can only download a file; it cannot send one to a cloud drive. Claude, working in a session that has a Google Drive connection, can. This procedure is what "back up to Google Drive" means. Nothing is uploaded without the user asking in that turn, and Claude says which folder and which files before it uploads. The Drive connection is used for this folder only: Claude does not browse, read or change anything else in the user's Drive.

## What, when, naming

| Item | Rule |
|---|---|
| What | the Backup JSON and the Rebuild kit zip from Data and backup, the folder data_offpage/ (datasets the page does not load, BACKUP_FORMAT.md section 8a), plus a text copy of the manifest hashes |
| When | on the 1st of each month (a reminder; SCHEDULED_TASKS.md), and before any upgrade or restore test |
| Names | `dashboard-backup-YYYY-MM-DD.json`, `dashboard-kit-YYYY-MM-DD.zip` (written by the page), `CHECKSUMS.sha256` (made by Claude), inside the folder `Money Ops Backup YYYY-MM-DD` |
| Where | `Money Ops Backups` in the user's own Drive, private, never shared by link |
| Verify | `node tools/verify_backup.js <file>` on each file before upload, and a name and size comparison after |
| Restore | download the newest folder, verify, then BACKUP_FORMAT.md section 8 |
| Optional | Drive is optional. Without it the user keeps the two files in a private folder of their own |

## Definition of done

A Google Drive backup is done when all of these are true:

1. A new folder named `Money Ops Backup YYYY-MM-DD` exists inside the user's backup folder (`Money Ops Backups`), created for this backup.
2. It contains the backup JSON and the rebuild kit zip of that day, a copy of the data_offpage files, and a copy of the checksums (the manifest's sha256 lines in a text file `CHECKSUMS.sha256`).
3. `node tools/verify_backup.js` passed on both files before upload.
4. After upload, the file names and sizes in the folder match the local files.
5. The page's "last saved" date was confirmed by the user clicking Save backup on the page (the page cannot know about Drive), or the user told Claude to record it.
6. The user was told in one or two sentences what was saved, where, and the verify result.

## Procedure

1. Ask the user for the day's files: Backup (JSON) and Rebuild kit buttons on Data and backup, or "the monthly backup kit" in chat. Never read the data from the live dashboard behind the user's back.
2. Verify both files locally with the tool. If a check fails, stop and say which; do not upload a broken backup.
3. Find or create the folder `Money Ops Backups` in Drive. Inside it create `Money Ops Backup YYYY-MM-DD` (new each month; never overwrite an older folder).
4. Upload the two files and `CHECKSUMS.sha256`.
5. List the new folder and compare names and sizes.
6. Record the date: the monthly task "Backup" moves to Done with the date; the next one is due on the 1st of next month (SCHEDULED_TASKS.md).
7. Keep the last 12 monthly folders; ask before deleting any older folder.

## Restore from Drive

Download the newest folder, run the verify tool, then follow BACKUP_FORMAT.md section 8.

## Privacy

The backup holds the user's financial figures. Keep the folder private (not shared by link). Do not put statements or original PDFs in the same folder unless the user wants that. Do not share the folder with anyone without the user saying so.

## When Drive is not connected

Say so, and give the two fallbacks: the user downloads both files and drops them in the folder, or the user attaches them in a chat that has Drive.

## Scheduled reminder

One reminder, on the 1st of each month: "Monthly backup due: open Data and backup, save the JSON and the kit, and tell Claude to put them in Drive." SCHEDULED_TASKS.md has the text for a scheduled task. If the account has no scheduled tasks, use a data_queue item dated the 1st. Never both.
