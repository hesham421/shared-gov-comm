# ADR-INV-034 — The stock count entry commits line by line
Status      : ACCEPTED
Stage       : P3.2 (Frontend — UX Design + Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : SCR-INV-006, REQ-INV-087, REQ-INV-088, REQ-INV-089, REQ-INV-090, REQ-INV-091, REQ-INV-092, AC-INV-097, AC-INV-099, AC-INV-100, API-INV-028, API-INV-029, API-INV-030, API-INV-031, API-INV-032, ADR-INV-032

## Context
The screen-composition rule says a screen opened to edit ONE record has ONE save. A page also keeps its child
lines inline, and their edits return to the form state until that one save. The stock count is designed
differently in the SRS and the API document. "Start count" creates the DRAFT (API-INV-028). Each scan **adds to
the server line** of that Variant (REQ-INV-088, AC-INV-097: scanning 1 and then 2 gives one line of 3). Changing
or removing a line is its own operation (API-INV-030 / 031). There is no operation that saves a whole count with
its lines, and no header field can change after "Start count". If scans were collected in the browser and saved
once, a phone that loses its page loses the count. Repeated scans could not add up on the server either.

## Decision
- SCR-INV-006 is a `page` with its lines `inline`, as the container rule says. Its submit is **the line
  action**. Each scan sends one API-INV-029 call. The one-line drawer opened from a line (change countedQty or
  countedMeasureQty, or remove) sends one API-INV-030 or 031 on confirm. One user action never sends two calls.
- "Start count", "Post" and "Cancel" are state transitions of the document, not saves. Post and Cancel carry the
  version the screen last read (ADR-INV-032).
- After each line call the screen re-reads the count (API-INV-027). It shows the server's line, not a local copy.

## Consequences
- The count entry has no form-level Save button. The one-save rule holds per action, and no surface holds unsaved
  line edits.
- The one-line drawer of a count line saves on confirm. This differs from the transfer and adjustment drawers,
  which return a value to the form. The F4 block of SCR-INV-006 states this.
- No REQ, RULE, field or API changes.

## Status
ACCEPTED — non-breaking.
