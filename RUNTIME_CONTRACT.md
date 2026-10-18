# RUNTIME_CONTRACT: how the Artifact runtime behaves, as observed

Observed on contract version 0.2.67. These are facts about the store and the runtime. Each one broke a build once. Treat them as requirements, and re-check them with the Stage 0 probe when the contract version differs (ARCHITECTURE.md sections 2.2, 2.3, 14 and 17).

## The facts

| # | Fact | What to do |
|---|---|---|
| 1 | Nested objects and arrays arrive as JSON text. A cell shaped {value: scalar} is unwrapped to the scalar | read nested cells only through a helper (parse in try/catch); keep nested cells to the allow-list (config datasets) |
| 2 | Only a non-empty array of plain row objects loads (or an object whose rows, data or results key holds one) | never ship an empty file; use a starter row with every value null |
| 3 | An empty dataset needs a starter row or empty-state handling | calcs strip the starter row (`realRows`) |
| 4 | A failed calc cascades: every calc that lists it as an input reports "<id> didn't run" | each calc guards its reads and returns a Needs row, never throws |
| 5 | A missing key arrives as null; every row has the union of all columns | test for null; never test `=== undefined` |
| 6 | CSV numbers arrive as text | datasets are JSON |
| 7 | Calc inputs arrive in the order listed as fresh shallow copies; results are structured clones | return new objects; functions and undefined are lost |
| 8 | The first draw can happen before every dataset has arrived | draw functions tolerate missing inputs and run again |
| 9 | Page parts run as separate function scopes | share helpers through one shared object |
| 10 | No test date global on live | one TODAY() helper; the tests replace it |
| 11 | 256 KB limit per store document; page parts under 200 KB | split large datasets and code |
| 12 | Document titles are 30 characters at most | the catalogue test enforces it |
| 13 | One asset per publish call | publish datasets one by one, then docs, then page parts, shell last |
| 14 | Writes are pinned to the version last read | read, change, write with the version; on mismatch re-read |
| 15 | The page cannot write datasets | the per-user db subtree and a "tell Claude" box are the only write paths |
| 16 | `position: fixed` and `100vh` are not reliable in the frame | the bottom bar is a measured app shell |
| 17 | Browser storage may be blocked or empty | only per-viewer conveniences; always try/catch |

## Capabilities

| Capability | Use | When absent |
|---|---|---|
| db | private overlay for task actions: `data/users/<id>/tasks/edits`; last backup date at `data/users/<id>/backup` | every action shows a ready one line message in a text box ("Task update for Claude: ...") |
| user | the viewer's id for the private subtree | no overlay, the text box fallback |
| downloads | save the backup JSON and kit | the Fallback line: ask Claude for "the monthly backup kit" |

Every capability use is wrapped in try/catch and has a fallback.

## Parser and calc engine in generic terms

```
cell(v):  if v is null or not an object: scalar(v)
          if v is not an array and "value" in v and typeof v.value is not object: scalar(v.value)
          else JSON.stringify(v)
rows(file): parse; if object take the first array in rows, data or results;
            fail if no array, empty array, or any element is not a plain object
            columns = union of keys; each row gets every column, absent keys are null
```

Calc engine: inputs delivered in the order listed, each a fresh shallow copy; result structured-cloned; an exception stores an error for that calc; a calc with an errored input does not run and reports "<id> didn't run."

## The harness

tools/runtime/runtime_extract.js holds the parser and engine rules extracted from the runtime; tools/engine.js runs the page's calcs through them without a browser. The harness must fail the build when any calc errors, any dataset file would be rejected, any document exceeds 256 KB, any title exceeds 30 characters, or any part reads a test-only global. A build that has not passed it is not published.

## The write flow for any new fact

1. Read the dataset document and note its version. 2. Edit the JSON. 3. Upload the asset (one per call). 4. Update the document pinned to the version read. 5. On a mismatch re-read and redo. 6. Re-run the correlation checks. 7. Republish page parts only if code changed. 8. Have a second pass recompute the changed number and what depends on it.

## Limits seen

| Limit | Value |
|---|---|
| Store document | 256 KB |
| Page part | 200 KB (selftest.js 60 KB) |
| Dataset title | 30 characters |
| Assets per publish call | 1 |
| Batch of document writes | 1 MiB |
| Rendered page | 16 MB (never close) |
