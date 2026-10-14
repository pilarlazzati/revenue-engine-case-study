# Revenue Engine: case study

A pipeline system for Dalai Group. It reads live sales records, shows where each opportunity stands, and checks its own numbers. Phase 1 is read-only on purpose: get the data right before anything acts on it.

The code is private because it works against real contact records. This page shows how it is built.

## What it does

- Loads pipeline records from a spreadsheet and shows them in a searchable, filterable table
- Shows each record's status, last touch and next step
- Runs a verification report: source count vs imported count, header discrepancies, date handling
- Lets the workbook be replaced without a code change, and rejects a bad file before it goes live

## Architecture

```
Spreadsheet (private)          React UI (passcode gate + table)
        |                                   ^
        v                                   | passcode header
Data source interface  ------>  FastAPI  /api/pipeline/*
(Excel today, Google Sheets
 can be added later)
```

Data loading sits behind one interface, so the source can change without touching the API or the UI.

## Design decisions

- **Never infer data.** Blanks stay blank. Only seven named columns are read; anything extra is ignored and reported.
- **Ambiguous dates are flagged, not guessed.** Clean dates are normalized for display, and the original value is kept.
- **Fail with a clear message.** Missing file, sheet or headers each return a specific error.
- **Read-only first.** No dashboard, AI, editing or sending until the data layer is verified.

## Security

- Every data endpoint requires a passcode held only in a server environment variable, compared in constant time
- Real records, spreadsheets and environment files are excluded from version control
- Secrets were scrubbed from the repository history and the passcode was rotated

## Tests

Backend test suite (pytest), all passing at the last review.

## Stack

Python, FastAPI, React, openpyxl.

---

Part of a larger system: [Dalai Group](https://dalaigroup.com) builds AI revenue systems and agent departments.
