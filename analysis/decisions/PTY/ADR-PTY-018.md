# ADR-PTY-018 — Physical precision of fields the SRS leaves open

Status  : ACCEPTED (non-breaking) · Stage : P2 · Module : PTY v1 · Date : 2026-10-29
Filed   : verbatim from the db-script §5.2 (analysis/modules/PTY/P2/db-script-pty.md) by the P3.1 repair, ADR-PTY-030

## Context
SRS A3 types creditLimit, saleAmount, creditLimitAtRequest and openExposure as `decimal` without precision, paymentTermDays as `number (0 – 365)` (a type the syntax map lacks), and the lookup-code fields as `lookup` without length. The platform serves Egypt and the Gulf, where some base currencies (OMR, KWD, BHD) carry 3 minor-unit decimals.

## Decision
Monetary decimals → `NUMERIC(18,3)` (15 integer digits, 3 decimals — covers every base currency of the target markets). Whole-number days → `INTEGER`, declared once in the script header. Lookup code columns → `VARCHAR(50)` (longest seeded code: EXPOSURE_UNAVAILABLE, 20 characters; room for open-lookup values added as data).

## Consequences
A 2-decimal currency stores a trailing zero digit; the backend rounds to the tenant currency's minor unit. No SRS meaning changes.

## Traces
ENT-PTY-002.creditLimit, ENT-PTY-002.paymentTermDays, ENT-PTY-003.paymentTermDays, ENT-PTY-005.saleAmount / creditLimitAtRequest / openExposure, DBF-PTY-014, 015, 016, 026, 034, 051, 052, 053, 054, 063; REQ-PTY-064, REQ-PTY-065, REQ-PTY-091.
