# Initializer

This file is the bootloader for restoring the budgeting system canvases from a tarball.

# Purpose

Expand the uploaded tarball, verify each listed file against its SHA-256 hash, create the corresponding canvas for each file, and load the file contents exactly into that canvas.

Do not summarize, normalize, repair, reorder, reformat, or rewrite the file contents while loading them into canvases.

This initializer distinguishes between:

- source-file checksum verification, which verifies the extracted tarball file before loading;
- canvas-content checksum verification, which verifies the created canvas contents after loading, if exact canvas readback is available in the current ChatGPT environment.

If exact canvas readback checksum verification is not available, the loader must report that limitation clearly and must not claim that the canvas contents themselves were checksum-verified.

# Global instructions

1. Expand the uploaded tarball.
2. Read this Initializer file carefully from beginning to end.
3. Before executing any file-loading steps, answer the verification question in the Initializer verification block.
4. Verify every file listed in the manifest against its provided SHA-256 hash.
5. Infer each canvas name from the file name using the canvas-name inference rules below.
6. For each file whose extracted source-file hash matches the manifest, create a canvas with the inferred canvas name.
7. Load the full file contents into that canvas exactly as extracted from the tarball.
8. After each canvas is created, determine whether exact canvas readback checksum verification is available in the current ChatGPT environment.
9. If exact canvas readback checksum verification is available, read back the full canvas contents exactly, compute the SHA-256 checksum of the readback contents, and compare that checksum to the manifest checksum.
10. If exact canvas readback checksum verification is not available, do not attempt to simulate, infer, approximate, or pretend to perform canvas-content checksum verification.
11. Track the authority status of each canvas using the Canvas authority rule below.
12. If a file hash does not match, do not load that file into a canvas. Report the mismatch and stop for that file.
13. If a file is missing, do not guess or reconstruct it. Report the missing file.
14. If the inferred canvas name is unclear, stop and ask the user.
15. After all files have been processed, print the final verification report described below.

# Initializer verification block

Before executing the instructions in this file, answer this question:

What is the final file listed in the manifest, and what canvas name should be inferred from it?

Expected answer:

- Final file: system.md
- Inferred canvas name: System

# Canvas-name inference rules

Infer the canvas name from the file name as follows:

- Remove the file extension.
- Replace underscores with spaces.
- Convert the result to title case.
- Preserve known domain terms and acronyms where appropriate.
- Use the explicit canvas-name mapping below when listed.

# Explicit canvas-name mapping

| File | Canvas name |
|------|-------------|
| adjustments_table.md | Adjustments Table |
| command_design.md | Command Design |
| debt_table.md | Debt Table |
| income_table.md | Income Table |
| migration_status.md | Migration Status |
| monthly_schema.md | Monthly Schema |
| monthly_system.md | Monthly System |
| planned_spending_table.md | Planned Spending Table |
| recurring_bills_table.md | Recurring Bills Table |
| receipt_code_glossary.md | Receipt Code Glossary |
| reserves_table.md | Reserves Table |
| schedule_table.md | Schedule Table |
| setup_budget_cells.md | Setup Budget Cells |
| setup_schema.md | Setup Schema |
| setup_system.md | Setup System |
| system.md | System |

# Canvas readback checksum verification

After each canvas is created, determine whether the current ChatGPT environment provides a reliable way to read back the exact full contents of that canvas and compute a SHA-256 checksum from the readback contents.

If exact canvas readback checksum verification is available:

1. Read back the full canvas contents exactly.
2. Compute the SHA-256 checksum of the readback canvas contents.
3. Compare the canvas checksum to the manifest checksum for the source file.
4. If the canvas checksum matches, mark the canvas as canvas-checksum verified.
5. If the canvas checksum does not match, mark the canvas as NOT AUTHORITATIVE, report the expected checksum, report the computed canvas checksum, and do not use that canvas for budgeting workflows unless the user explicitly approves continuing.

If exact canvas readback checksum verification is not available:

1. Do not attempt to simulate, infer, or approximate canvas checksum verification.
2. Mark the canvas-content checksum verification field as NO.
3. State that the extracted source file passed checksum verification before loading, but the resulting canvas contents could not be independently checksum-verified after creation.
4. Do not claim that the canvas itself was hash-verified.
5. Treat the canvas as loaded from a verified source file, not as an independently checksum-verified canvas, unless the user explicitly accepts this limitation.

# Canvas authority rule

A loaded canvas may be treated as fully checksum-verified only if its contents were read back after creation and its SHA-256 checksum matched the manifest checksum.

If canvas readback checksum verification is not available in the current ChatGPT environment, report that limitation clearly.

In that case, the loader may still load canvases from verified source files, but it must distinguish:

- Source-file checksum verified: YES
- Canvas-content checksum verified: NO
- Canvas loaded from verified source: YES
- Canvas independently verified after loading: NO

Use one of these authority statuses for every file:

- FULLY VERIFIED: the extracted source file hash matched the manifest, the canvas was created, the canvas contents were read back exactly, and the canvas readback hash matched the manifest.
- LOADED FROM VERIFIED SOURCE ONLY: the extracted source file hash matched the manifest and the canvas was created, but exact canvas readback checksum verification was not available.
- NOT AUTHORITATIVE: the file was missing, the extracted source-file hash failed, the canvas was not created, or the canvas readback hash failed.

Do not execute any budgeting workflow from a NOT AUTHORITATIVE canvas unless the user explicitly approves continuing with partial or unverified data.

If all canvases are LOADED FROM VERIFIED SOURCE ONLY, the system may use them only after clearly reporting that exact canvas readback checksum verification was unavailable and that the canvases are not independently canvas-checksum verified.

# Manifest

| SHA-256 | File | Canvas name |
|--------|------|-------------|
| 91eb446ebcfb0abd8a1146a925b2c7ac721801614fd0ee9a428e521bde7ef8b0 | adjustments_table.md | Adjustments Table |
| ceb7dc8433bae2139c05998b694ad56aef45a33d572cfdf498f6a1039535f40b | command_design.md | Command Design |
| cf677cec72b6a2a6de4c0dc2616af819ec9361e73699991dc9d5b5acf6bd5dcd | debt_table.md | Debt Table |
| 5dca339e006656dc64846bca40b77de141c43e5497c95c87559f45a1baf48df0 | income_table.md | Income Table |
| f97a8b8987018357e243f0fdfc7146a79bb402281d4c629264bd398c3321f742 | migration_status.md | Migration Status |
| 9588ebe37c3adb09fe69fe1ad012ee052cc04d6a968f905c50161105d7a0d92e | monthly_schema.md | Monthly Schema |
| e7692005ebea12879a27ac520dcbd2a19cf5b8b087b4a11c69fc5abe4be86c64 | monthly_system.md | Monthly System |
| 5704c996cc768c02eb56c77cfd37c0308968544dfb109e42d8d1f4a784d6d389 | planned_spending_table.md | Planned Spending Table |
| 3fdbbe18fc99d773330facecc9f6e85739b3a5396bc07cd36f937af4a92e392a | recurring_bills_table.md | Recurring Bills Table |
| 5c617b044342f2864b4073507602a4f835df33c04784bac28144a48d9fac8000 | receipt_code_glossary.md | Receipt Code Glossary |
| e8ed1ebfd4e4ecebbd263bec12133f87aae649fb4bf4b545abd815c822d15dac | reserves_table.md | Reserves Table |
| 9cca483a69df5ee8232f37bcfdb7038b0c32efe0ba47d0341f36578535217f4d | schedule_table.md | Schedule Table |
| efafbbf4160dffa773828dfad65cc3c01534f29c3c7dd4afbb2b344b025d43f0 | setup_budget_cells.md | Setup Budget Cells |
| ea067971d627f17f93fbf2cfbb8b0a3e71d3e1c5074ad81c2e705dd0bc329667 | setup_schema.md | Setup Schema |
| d925fa394df71ed86979d7f2e0a705ba3d82769a9d58002ed06141f040b4f75c | setup_system.md | Setup System |
| 55dde417dc642caa5dcc3fe61a0d4cac62793d216132a9711e695646ab49d918 | system.md | System |

# Per-file loading procedure

For each manifest row, perform the following steps in order:

1. Confirm the file exists in the extracted tarball.
2. Compute the SHA-256 hash of the extracted file exactly as stored.
3. Compare the computed source-file hash to the manifest hash.
4. If the source-file hash matches:
   - Create a canvas using the mapped canvas name.
   - Copy the full file contents into the canvas exactly.
   - Do not alter whitespace, table alignment, spelling, capitalization, headings, or punctuation.
   - Determine whether exact canvas readback checksum verification is available.
   - If available, read back the canvas contents exactly and compute the canvas SHA-256 checksum.
   - Compare the canvas SHA-256 checksum to the manifest checksum.
   - Assign the appropriate authority status.
5. If the source-file hash does not match:
   - Do not create or update the canvas for that file.
   - Report the expected hash.
   - Report the computed source-file hash.
   - Mark the file as FAILED HASH CHECK.
   - Mark the authority status as NOT AUTHORITATIVE.
6. If the file is missing:
   - Do not create or update the canvas for that file.
   - Mark the file as MISSING.
   - Mark the authority status as NOT AUTHORITATIVE.
7. Continue processing the remaining files unless the user explicitly instructed otherwise.

# File-specific verification blocks

Use these blocks to prove each file was read and to provide a predictable report.

## adjustments_table.md

Canvas name: Adjustments Table

Verification question:

What are the column headers in adjustments_table.md?

Expected answer type:

List the headers exactly as they appear.

## command_design.md

Canvas name: Command Design

Verification question:

What is the final heading in command_design.md?

Expected answer type:

Quote the final heading exactly.

## debt_table.md

Canvas name: Debt Table

Verification question:

What is the last debt item listed in debt_table.md?

Expected answer type:

Quote the item name exactly.

## income_table.md

Canvas name: Income Table

Verification question:

What are the column headers in income_table.md, and what is the Transient value for Regular paycheck?

Expected answer type:

List the headers exactly as they appear, then quote the Transient value exactly.

Expected answer:

- Headers: Item | Expected | Actual | Transient | Notes |
- Transient value for Regular paycheck: FALSE

## migration_status.md

Canvas name: Migration Status

Verification question:

What is the migration status?

Expected answer type:

Quote the status sentence exactly.

Expected answer:

Migration is complete.

## monthly_schema.md

Canvas name: Monthly Schema

Verification question:

What values may the Schedule Payday field contain?

Expected answer type:

Quote the allowed values exactly.

Expected answer:

TRUE, FALSE, or UNKNOWN

## monthly_system.md

Canvas name: Monthly System

Verification question:

What are the two high-level rule sections in monthly_system.md?

Expected answer type:

List the two section headings exactly.

Expected answer:

- Behavioral Rules
- Human Notes

## planned_spending_table.md

Canvas name: Planned Spending Table

Verification question:

Which Planned Spending row has Recurring Bill set to TRUE and Reserve set to Joseph?

Expected answer type:

Quote the item name exactly.

## recurring_bills_table.md

Canvas name: Recurring Bills Table

Verification question:

What is the final recurring bill item listed in recurring_bills_table.md?

Expected answer type:

Quote the item name exactly.

## receipt_code_glossary.md

Canvas name: Receipt Code Glossary

Verification question:

What does RB CRH 26.7 mean?

Expected answer type:

Quote the meaning exactly.

Expected answer:

Raisin Bran Crunch

## reserves_table.md

Canvas name: Reserves Table

Verification question:

What is the Paused value for Spain fund?

Expected answer type:

Quote the value exactly.

## schedule_table.md

Canvas name: Schedule Table

Verification question:

What are the Month, Day, and Payday values for Regular paycheck?

Expected answer type:

Quote the three values exactly.

Expected answer:

FALSE, FALSE, TRUE

## setup_budget_cells.md

Canvas name: Setup Budget Cells

Verification question:

What is the final row item or final meaningful entry in setup_budget_cells.md?

Expected answer type:

Quote it exactly.

## setup_schema.md

Canvas name: Setup Schema

Verification question:

What is the final schema column or final validation rule listed in setup_schema.md?

Expected answer type:

Quote it exactly.

## setup_system.md

Canvas name: Setup System

Verification question:

What is the final rule or final heading in setup_system.md?

Expected answer type:

Quote it exactly.

## system.md

Canvas name: System

Verification question:

What fields must be printed for each receipt item during receipt analysis?

Expected answer type:

List the fields exactly as described.

Expected answer:

The code or item text as it appears on the receipt, what the item appears to be, the proposed budget category, and the price.

# Final verification report

After processing all files, print a final report with the following sections.

## Initializer verification

- Verification question answered: YES or NO
- Answer provided
- Expected answer
- Match: YES or NO

## Manifest verification summary

- Total files listed
- Files found
- Files missing
- Files with matching source-file SHA-256 hashes
- Files with failed source-file SHA-256 hashes
- Canvases created
- Canvases skipped
- Exact canvas readback checksum verification available: YES / NO / MIXED
- Canvas contents checksum-verified
- Canvas contents not checksum-verified
- Fully verified authoritative canvases
- Canvases loaded from verified source only
- Not-authoritative canvases

## Canvas checksum verification summary

- Exact canvas readback checksum verification available: YES / NO / MIXED
- Canvas contents checksum-verified: count
- Canvas contents not checksum-verified: count
- Fully verified authoritative canvases: count
- Canvases loaded from verified source only: count
- Not-authoritative canvases: count

If exact canvas readback checksum verification was not available, include this statement:

Exact canvas readback checksum verification was not available. The extracted source files passed checksum verification and were loaded into canvases, but the resulting canvas contents were not independently hash-verified. Treat these canvases as loaded from verified source files, not as checksum-verified canvases, unless the user explicitly accepts this limitation.

## Per-file results

For each file, print:

- File
- Expected SHA-256
- Computed source-file SHA-256
- Source-file hash result: PASS / FAIL / MISSING
- Inferred canvas name
- Canvas created: YES / NO
- Verification question
- Verification answer
- Was exact canvas readback checksum verification available? YES / NO
- Canvas SHA-256, if available
- Canvas hash result: PASS / FAIL / NOT AVAILABLE
- Was the checksum of the canvas contents verified before treating it as authoritative? YES / NO
- Authority status: FULLY VERIFIED / LOADED FROM VERIFIED SOURCE ONLY / NOT AUTHORITATIVE

## Failed files

List every file that failed or was missing.

If none failed, say:

All extracted source files passed manifest verification and were loaded into canvases.

If all canvases were also read back and checksum-verified, additionally say:

All canvases were read back, checksum-verified, and may be treated as fully verified authoritative canvases.

If canvas readback checksum verification was not available, additionally say:

Canvas readback checksum verification was not available. The canvases were loaded from verified source files, but the canvas contents themselves were not independently checksum-verified.

## Canvas authority status

If canvas readback checksum verification was available and all canvas hashes matched, say:

All canvases were read back, checksum-verified, and may be treated as fully verified authoritative canvases.

If canvas readback checksum verification was not available, say:

Canvas readback checksum verification was not available. The extracted source files passed checksum verification and were loaded into canvases, but the resulting canvas contents were not independently hash-verified. Treat the canvases as loaded from verified source files, not as checksum-verified canvases, unless the user explicitly accepts this limitation.

If any canvas readback checksum failed, say:

One or more canvases failed readback checksum verification and must be treated as NOT AUTHORITATIVE unless the user explicitly approves continuing.

# Final safety rules

- Do not repair files during loading.
- Do not normalize table formatting during loading.
- Do not convert Markdown tables to TSV during loading.
- Do not infer missing files.
- Do not create replacement contents for missing or failed files.
- Do not execute any budgeting workflow until all required canvases have passed source-file verification and their authority status has been reported.
- Do not claim that a canvas is fully checksum-verified unless exact canvas readback checksum verification was performed and passed.
- If canvas readback checksum verification is unavailable, say so clearly.
- Do not treat lack of canvas readback checksum verification as a source-file hash failure.
- Do not hide the difference between source-file verification and canvas-content verification.

