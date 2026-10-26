# ADR-CAT-018 — Seams that erp-core 1.6.0 does not provide are platform-owned: variant attributes and item Excel import in CAT, lot and dual quantity in INV, cash and bank in STL, the tax adapter in TAX
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: 154eb23481a1
traces      : POL-CAT-007

## Decision
traces: OI-06, CAND-CAT-003, CAND-INV-007, POL-CAT-007
Accept, and extend it. The 1.6.0 manifest lists no custom-attributes, lot, dual-quantity, cash-and-bank or tax-adapter capability. Round 1 missed one more: POL-CAT-007 needs an Excel import. The manifest offers `uploads` (file), `validation` (common) and `csv-export` (report), but no import. CAT therefore owns the item import. It uses `file` for the upload and `common` for validation, and creates no new entity. PTY and the other modules decide their own imports when they are requested. Re-match on the next given upgrade.
Sources: manifest §1.5; KB §4.1, §5, §6.9.

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md
