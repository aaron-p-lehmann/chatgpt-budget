# Initializer

This file is the bootloader for restoring the budgeting system canvases from a tarball.

# Purpose

Expand the uploaded tarball, verify each listed file against its SHA-256 hash, create the corresponding canvas for each file, and load the file contents exactly into that canvas.

Do not summarize, normalize, repair, reorder, reformat, or rewrite the file contents while loading them into canvases.

# Global instructions

1. Expand the uploaded tarball.
2. Read this Initializer file carefully from beginning to end.
3. Before executing any file-loading steps, answer the verification question in the Initializer verification block.
4. Verify every file listed in the manifest against its provided SHA-256 hash.
5. Infer each canvas name from the file name using the canvas-name inference rules below.
6. For each file, create a canvas with the inferred canvas name.
7. Load the full file contents into that canvas exactly as extracted from the tarball.
8. Do not treat any loaded canvas as authoritative until its hash has been verified.
9. If a file hash does not match, do not load that file into a canvas. Report the mismatch and stop for that file.
10. If a file is missing, do not guess or reconstruct it. Report the missing file.
11. If the inferred canvas name is unclear, stop and ask the user.
12. After all files have been processed, print the final verification report described below.

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
| migration_status.md | Migration Status |
| monthly_schema.md | Monthly Schema |
| monthly_system.md | Monthly System |
| planned_spending_table.md | Planned Spending Table |
| recurring_bills_table.md | Recurring Bills Table |
| reserves_table.md | Reserves Table |
| schedule_table.md | Schedule Table |
| setup_budget_cells.md | Setup Budget Cells |
| setup_schema.md | Setup Schema |
| setup_system.md | Setup System |
| system.md | System |

# Manifest

| SHA-256 | File | Canvas name |
|--------|------|-------------|
| 06d66de31b7899470eaa7c10df440d3f7ebdcac27dfc761b2ebf65b4df626e3f | adjustments_table.md | Adjustments Table |
| 2bfffe7a391d3937a13d41237209f130c6d84ae017d60bec975d873b0d66cc96 | command_design.md | Command Design |
| 2b59dea76850e7ddf5213c4aec263adadff82830b4b0eced56509d93339e9309 | debt_table.md | Debt Table |
| 9444fee363ef91ec970a2843bcf46477e02f0d10f0aa36a4abe2930c28306f4f | migration_status.md | Migration Status |
| a9fa347c335c1f9fd9b2f2e9745a32612370c040df1ff609e78657782c2c3096 | monthly_schema.md | Monthly Schema |
| 1d033535b63c043f004b9f5d9063f9d7fa4f081d981b6ded66ec8573b3183414 | monthly_system.md | Monthly System |
| 653b4d6571fbd70214fe64dd23779095e7f74536e909890dba0bec070ecb7cf6 | planned_spending_table.md | Planned Spending Table |
| 6da9c15e47f7a7e451a61f4963de08804cff254f94f663d781491790b04c20a5 | recurring_bills_table.md | Recurring Bills Table |
| 103b961b3e9b448c2e2b190066f822596d5532719dca7d270e8deb52f0d6d4d5 | reserves_table.md | Reserves Table |
| 34e44434c2a301e746d883db2f318b9c1f208c93ed7b729ffb5848eb21fe13a3 | schedule_table.md | Schedule Table |
| efafbbf4160dffa773828dfad65cc3c01534f29c3c7dd4afbb2b344b025d43f0 | setup_budget_cells.md | Setup Budget Cells |
| ea067971d627f17f93fbf2cfbb8b0a3e71d3e1c5074ad81c2e705dd0bc329667 | setup_schema.md | Setup Schema |
| d925fa394df71ed86979d7f2e0a705ba3d82769a9d58002ed06141f040b4f75c | setup_system.md | Setup System |
| cb953158064049e82da77e0f8e28c0c3b515646af1b0a05b9c960d22675ddaf9 | system.md | System |

# Per-file loading procedure

For each manifest row, perform the following steps in order:

1. Confirm the file exists in the extracted tarball.
2. Compute the SHA-256 hash of the extracted file exactly as stored.
3. Compare the computed hash to the manifest hash.
4. If the hash matches:
   - Create a canvas using the mapped canvas name.
   - Copy the full file contents into the canvas exactly.
   - Do not alter whitespace, table alignment, spelling, capitalization, headings, or punctuation.
5. If the hash does not match:
   - Do not create or update the canvas for that file.
   - Report the expected hash.
   - Report the computed hash.
   - Mark the file as FAILED HASH CHECK.
6. If the file is missing:
   - Do not create or update the canvas for that file.
   - Mark the file as MISSING.
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

## migration_status.md

Canvas name: Migration Status

Verification question:

What is the final listed next step in migration_status.md?

Expected answer type:

Quote the final bullet under the next-steps section exactly.

## monthly_schema.md

Canvas name: Monthly Schema

Verification question:

What is the final glossary entry in monthly_schema.md?

Expected answer type:

Quote the final glossary entry exactly.

## monthly_system.md

Canvas name: Monthly System

Verification question:

What is the final semantic validation rule in monthly_system.md?

Expected answer type:

Quote the final bullet under semantic validation rules exactly.

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

## reserves_table.md

Canvas name: Reserves Table

Verification question:

What is the Paused value for Spain fund?

Expected answer type:

Quote the value exactly.

## schedule_table.md

Canvas name: Schedule Table

Verification question:

What is the Domain and Item of the final schedule row?

Expected answer type:

Quote both values exactly.

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

What is the final data type listed in system.md?

Expected answer type:

Quote the final data type definition exactly.

# Final verification report

After processing all files, print a final report with the following sections:

## Initializer verification

- Verification question answered: YES or NO
- Answer provided
- Expected answer
- Match: YES or NO

## Manifest verification summary

- Total files listed
- Files found
- Files missing
- Files with matching SHA-256 hashes
- Files with failed SHA-256 hashes
- Canvases created
- Canvases skipped

## Per-file results

For each file, print:

- File
- Expected SHA-256
- Computed SHA-256
- Hash result: PASS / FAIL / MISSING
- Inferred canvas name
- Canvas created: YES / NO
- Verification question
- Verification answer
- Was the checksum of the canvas contents verified before treating it as authoritative? YES / NO
  As of this version of GPT-5.5 Thinking, this isn't possible.  This is here so that future people know that.

## Failed files

List every file that failed or was missing.

If none failed, say:

All files passed verification and were loaded into canvases.

# Final safety rules

- Do not repair files during loading.
- Do not normalize table formatting during loading.
- Do not convert Markdown tables to TSV during loading.
- Do not infer missing files.
- Do not create replacement contents for missing or failed files.
- Do not execute any budgeting workflow until all required canvases have passed verification or the user explicitly approves continuing with partial data.

