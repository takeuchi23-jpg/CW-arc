---
name: chatwork-archive-completion
description: Create a Chatwork archive knowledge report, upload the final Markdown report to the approved Google Drive folder, then mark the matching Chatwork room as complete in the CW archive Google Sheet. Use when the user asks to process a Chatwork room/archive and also track completion, mark 完了/TRUE, update the archive spreadsheet, process a specific CW archive spreadsheet row, search Chatwork by the CW name in column A, continue from the next unchecked item, process the list from the bottom, or manage 社内ナレッジ蓄積 completion status.
---

# Chatwork Archive Completion

## Overview

Use this skill to run the complete archive workflow:

1. Extract or reuse a Chatwork room log.
2. Create the internal-knowledge Markdown report.
3. Upload the final report to the approved Drive folder.
4. Find the matching Chatwork room name in the archive spreadsheet and set its completion column to `TRUE`.

It can also run in continuous-list mode: after completing one unchecked row, identify the next unchecked row below-to-above or from the requested direction and continue only when the user has clearly requested continuous processing. In continuous-list mode, do not stop merely because one or two rows have been completed; keep processing additional unchecked rows until a defined stop condition is reached.

When the task is only report creation without spreadsheet completion, use `chatwork-archive-knowledge-report` directly. When the user mentions completion tracking, `TRUE/FALSE`, the archive spreadsheet, row updates after report creation, or a spreadsheet row number such as `432`, use this skill.

## Fixed Destinations

Default report Drive folder:

- Folder name: `CWアーカイブナレッジ格納場所`
- Folder URL: `https://drive.google.com/drive/folders/1sWGSed_ZqjTO7m0OFmn4-lAfkxbf67pg`

Completion spreadsheet:

- Sheet name: `CWアーカイブ`
- Spreadsheet URL: `https://docs.google.com/spreadsheets/d/14k28kP6Roai3eoiIAQdHS8KQMY2qC3XGOp4QHXoaxrA/edit?gid=0#gid=0`
- Expected columns:
  - Column A: Chatwork room name
  - Column B: completion flag, shown as `FALSE` or `TRUE`

## Processing Modes

### Single-item mode

Default mode. Process exactly one Chatwork room or spreadsheet row, upload the report, then mark the matching row as `TRUE`.

Use this when the user gives:

- A Chatwork URL.
- A specific spreadsheet row number.
- A request such as `この案件`, `この行`, `これを完了にして`.

### Continuous-list mode

Use this only when the user clearly asks to keep going through the spreadsheet list, for example:

- `リスト下からチェックがついていない案件からお願い`
- `チェックをつけたら次の未チェックも進めて`
- `下から順に未完了を処理して`
- `次のチェックを自動でつける`

In continuous-list mode:

1. Determine the current target row using the requested direction. If unspecified, use the bottom-most `FALSE` row.
2. Complete the full workflow for that row: extract Chatwork, create report, upload report, verify matching row, set column B to `TRUE`.
3. After verification, scan again for the next unchecked row in the same direction.
4. If another unchecked row exists, continue with that row only when the user's request explicitly asked for continuous processing.
5. When the user's request says to continue through the list, process at least two rows and continue to a third and subsequent rows when available. Do not treat two completed rows as a natural stopping point.
6. Stop and report when:
   - no unchecked row remains in the requested direction,
   - the next row's Chatwork room cannot be matched confidently,
   - Chatwork/Drive/Sheets requires user login or manual intervention,
   - report creation fails quality checks,
   - or the user interrupts with a new instruction.

Guardrail: never mark the next row `TRUE` merely because the previous row succeeded. Each row must complete report creation, Drive upload, and row-name verification before its own completion flag is updated.

Tab reuse guardrail for continuous-list mode:

- Complete the continuous run inside the smallest stable set of tabs opened by this skill run.
- Do not claim, reuse, navigate, or otherwise operate on browser tabs that were already open before this skill run started, because the user may still be using them.
- Once this skill run opens a Chatwork tab, reuse that same skill-owned Chatwork tab for every subsequent room search, room open, title check, and log extraction.
- Do not open a new Chatwork tab for each row unless the skill-owned Chatwork tab is closed, stale, unrecoverable, or blocked by login/manual intervention.
- Likewise, prefer reusing the skill-owned spreadsheet and Drive folder tabs for row checks, completion updates, and uploads.
- If an existing user-owned tab appears useful, ask the user before claiming or operating on it.
- If a skill-owned tab must be replaced, say why in a short progress update and then continue with the replacement tab as the new reusable tab.

## Workflow

### 1. Resolve the Source Room

If the user provides a spreadsheet row number instead of a Chatwork URL:

1. Open the completion spreadsheet URL.
2. Select and copy `A{row}:B{row}`.
3. Confirm column A contains the CW room name and column B is not already `TRUE`.
4. Use the copied column A value as the spreadsheet canonical name.
5. Open Chatwork only if a reusable, skill-owned Chatwork tab for the current run is not already available. If one is available, return to that same skill-owned tab and search the room list there with a distinctive part of the name, usually the customer name. Do not use a pre-existing user-owned Chatwork tab unless the user explicitly approves it.
6. Prefer an exact room-name result over a related room such as `HR/BESTO`, financing, or vendor rooms.
7. Open the matching Chatwork room and confirm the browser title or `#_roomTitle` matches the spreadsheet name after normalization.
8. Record the Chatwork `rid` from the URL and use it in output filenames.

Do not update column B merely because the user gave a row number. The row number is a starting point for reading the CW name; still verify the opened Chatwork room and final sheet row by name.

Useful row-number pattern:

- User: `スプレッド432に書いてあるCW名をCWから検索して、同じ作業`
- Read: `A432:B432`
- Search Chatwork by: customer name from `A432`, for example `佐々木由美`
- Open exact room: `47期1月引渡済【BH】佐々木由美様/本宅新築工事/案件/山形市円応寺　担当：メイン國井`
- Complete: create report, upload it, then set the verified matching row's B cell to `TRUE`.

### 2. Create or Locate the Report

If the user provides a Chatwork room URL or asks to analyze a room:

- Use `chatwork-archive-knowledge-report`.
- Follow its extraction, report structure, evidence quoting, quality checks, and Drive upload requirements.
- Save the final Markdown report in `outputs/`.
- Upload the Markdown report, not raw logs, unless the user explicitly requests raw logs.

If the user only asks to mark completion for a report that was just created:

- Reuse the latest matching Markdown report in `outputs/`.
- Do not re-extract Chatwork logs unless the user asks for a revised report or the existing report is missing/stale.

When the Chatwork room was found from a spreadsheet row:

- Treat the Chatwork room title as the report's canonical room title.
- Preserve the Chatwork title in the report and raw extraction.
- Use the spreadsheet row value only for matching and completion verification.

### 3. Determine the Canonical Chatwork Room Name

Use the room title from the extraction/report as the canonical name.

Normalize names for matching:

- Convert full-width spaces and tabs to normal spaces.
- Collapse repeated whitespace.
- Trim leading/trailing whitespace.
- Ignore the `Chatwork - ` browser title prefix.
- Treat visually identical names as matching even when spacing differs.

Example:

- Chatwork title: `Chatwork - 47期11月3日引渡済【BH】難波翔汰様/本宅新築工事/案件/東根　担当：メイン菊地　サブ竹内`
- Spreadsheet value: `47期11月3日引渡済【BH】難波翔汰様/本宅新築工事/案件/東根 担当：メイン菊地 サブ竹内`
- Result: same room; update the spreadsheet row for this value.

### 4. Find the Matching Spreadsheet Row

Prefer searching by Chatwork room name over trusting a user-provided row number.

Important guardrail:

- If the user gives a row number, verify that column A in that row matches the Chatwork room name before editing column B.
- Google Sheets row numbers can shift due to sorting/filtering or hidden rows. If a search shows the room at a different row, update the row found by name and explain the discrepancy.
- Never update a row whose column A is a different project name.

Reliable browser method:

1. Open the completion spreadsheet URL.
2. Use the sheet's find UI (`Cmd+F` on macOS) to search a distinctive part of the room name, such as the customer name.
3. Read the search result region; it should show a cell like `A435 <room name>`.
4. Confirm the found column A value matches the canonical room name after normalization.
5. Set column B of that same row to `TRUE`.

When selecting and copying a row for verification:

- Copy `A{row}:B{row}`.
- Confirm copied column A is the target room name and copied column B is `TRUE`.
- If a wrong row was touched during verification, restore its previous value before finishing.

If the task started from a row number:

- After report upload, return to that same row and copy `A{row}:B{row}` before editing if needed.
- If column A still matches the canonical room name after normalization, update `B{row}`.
- If it does not match, search the sheet by the room name and update only the found matching row. Report the row-number mismatch.

### 5. Update Completion

Set the matching row's column B to `TRUE`.

Acceptable update methods:

- Browser UI range navigation with `range=B{row}` and paste `TRUE`.
- Connected document-control tool, if a Google Sheets session is available.
- A Google Sheets API/connector, if available and authorized.

After editing:

- Wait until Sheets indicates the file is saved, or verify by copying `A{row}:B{row}`.
- Report the row number that was actually updated.
- Mention any row-number mismatch from the user's original statement.

### 5.5 Continue to the Next Unchecked Row, If Requested

After successfully setting `B{row}=TRUE` and verifying `A{row}:B{row}`:

1. Re-open or return to the completion spreadsheet.
2. Starting just above the completed row when processing from the bottom, find the next row where column B is not `TRUE`.
3. Copy `A{nextRow}:B{nextRow}` and confirm column A contains a Chatwork room name and column B is `FALSE` or blank.
4. Use that column A value as the next spreadsheet canonical name and repeat the full workflow from "Resolve the Source Room".
5. Continue beyond the second completed row when another unchecked row can be matched confidently and no stop condition applies.
6. Keep a concise running note of completed rows and rids for the final response.

If the next unchecked row is ambiguous or already appears to have a matching report uploaded, pause and explain the ambiguity instead of guessing.

### 6. Final Response

Keep the final response short:

- State that the report was created/uploaded if that was part of the task.
- State the spreadsheet row and value changed, for example `B435 = TRUE`.
- If the task started from a row number, state which row was read and which Chatwork `rid` was processed.
- If a user-provided row differed from the actual searched row, state that the matching Chatwork name was found at the actual row and the other row was not changed or was restored.
- If continuous-list mode was used, list the rows completed in this run and the next row still pending, if any.

## Quality Checks

Before finishing, confirm:

- The Markdown report exists in `outputs/`, if report creation was requested.
- The Drive upload succeeded, if report creation/upload was requested.
- The spreadsheet row was found by matching column A, not by row number alone.
- If the source came from a spreadsheet row, column A was copied before work and matched to the opened Chatwork room after normalization.
- The copied verification range shows target room name in column A and `TRUE` in column B.
- Any accidentally touched non-target row has been restored.
