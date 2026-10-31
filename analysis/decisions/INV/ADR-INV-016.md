# ADR-INV-016 — Physical types the SRS leaves open
Status      : ACCEPTED
Stage       : P2 (Database)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : DBF-INV-027, DBF-INV-042, DBF-INV-044, DBF-INV-045, DBF-INV-159, DBF-INV-177, DBF-INV-192, DBF-INV-193, DBF-INV-194, REQ-INV-051, REQ-INV-059, REQ-INV-071, REQ-INV-113

Filed from db-script-inv.md §5.2, where the decision is recorded in full; the decision is unchanged (its trace list is written out as ids).

## Context
SRS A3 gives the scale of every decimal (quantity and measure 3, unit and average cost 4, value 2 — D-06) but
no precision; it uses the logical types `date` and `number` (ENT-INV-011.rowNo), which the profile syntax map
lacks, and `lookup` without a length.

## Decision
- Precision 18 for every decimal: `NUMERIC(18,3)` quantities and measures, `NUMERIC(18,4)` costs,
  `NUMERIC(18,2)` values (15 / 14 / 16 integer digits — far above any trader's stock).
- Logical `date` → `DATE` (a calendar date; RULE-INV-038 compares the expiry date with today in the tenant
  time zone, which a timestamp would blur).
- Logical `number` → `INTEGER` (row numbers 1 … 1000, RULE-INV-027).
- Lookup codes → `VARCHAR(50)` (the given `mdl` convention for a stored code).
Each is declared once in the script header. The traces name representative columns; every other NUMERIC,
DATE and lookup column follows the same rule.

## Consequences
- Half-up rounding to the stated scale (SRS D-06) is the service's job; the database stores the scale exactly.
- A base currency with 3 minor units stores values to 2 decimals as SRS D-06 states; changing that is an SRS
  decision ("Change the places per tenant currency").

## Status
ACCEPTED — non-breaking.
