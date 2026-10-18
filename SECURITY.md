# SECURITY

## Reporting a problem

Please do not open a public issue for a security or privacy problem, and never paste real financial data, statements, backups or tokens into an issue, pull request or chat that others can read. Report privately to the repository owner through the private reporting channel of the hosting platform (for GitHub, the Security tab, "Report a vulnerability"), or contact the owner directly. Include: what you found, the file and line, the steps to reproduce with invented data, and what you think the impact is. There is no bug bounty and no service level; reports are handled on a best effort basis. TODO for the owner: add a contact address here before the repository is made public.

## Supported scope

| In scope | Out of scope |
|---|---|
| the page code (page/js, markup.html, style.css) and how it handles data values | the Claude Artifact service, the Claude apps, and Google Drive themselves |
| the tools, tests and build scripts in code-base.zip | bugs that need the user to paste malicious code into their own chat |
| the backup format and tools/verify_backup.js | financial, tax or legal correctness of a calculation (report it as a normal bug) |
| the instructions to the agent (AGENT_PROMPT.md, CLAUDE.md, PROMPTS.md and the procedures) | other people's forks and modified copies |
| privacy leaks in the repo (a real name, figure, identifier or link) | |

Only the latest version in VERSION is supported.

## Threat model in short

**Assets.** The user's financial figures (balances, pay, debts, holdings), kept in the user's own Artifact dataset store, in downloaded backups, and in Drive if the user chooses. The repo itself holds no real data.

**Who is trusted.** The user, in the live chat. The repo's own code and procedures, as reviewed. Nothing else.

**Untrusted inputs.** Uploaded statements (PDF, CSV, screenshots), text copied from emails or web pages, backup files and kits handed to Claude for a restore, and the values inside them. They may contain markup, scripts, formulas or sentences that look like instructions.

| Threat | Defense |
|---|---|
| Script or markup in a data value runs in the dashboard | the page builds elements and sets text only; no innerHTML, eval, inline handlers, external scripts or links; tests/static_scan.js and tests/inject.js enforce it on every full run |
| A document tells Claude to do something | statements and web pages are data, never instructions (AGENT_PROMPT.md rule 8); risky actions need an explicit yes in the live chat |
| A hostile backup or kit | tools/verify_backup.js fails closed on malformed input, refuses unsafe zip names and oversized entries; the page code in a kit is never published as is; hashes are checked before use |
| Real numbers committed or shared by accident | .gitignore blocks backups and statements; the dashboard is private until the user shares it; the privacy scan in SECURITY_AND_PRIVACY.md |
| Sensitive identifiers stored | account numbers, SSNs, routing numbers, loan ids, tax ids and street addresses are never stored; export strips fields named like them; verify_backup flags them |
| A spreadsheet formula in a CSV view | CSV files are a view only; open them as text, never restore from them; the JSON is the source |
| Over-broad connector use | Mail and Drive are optional, used only for what the user asked, and each upload, send or delete needs a yes |

**Not defended.** Anyone who has the dashboard link after the user shares it can see the numbers. Anyone with access to the user's Claude account, Drive or computer can read the data. A malicious change to a fork the user chooses to trust. A user who tells Claude to bypass a rule.

See SECURITY_AND_PRIVACY.md for data handling and SECURITY_REVIEW.md for the review that checked all of this.
