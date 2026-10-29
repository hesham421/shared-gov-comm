# ADR-INV-009 — Stock is valued at perpetual weighted-average cost per Variant across the tenant; transfers carry cost unchanged; FIFO is not offered in v1
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INV        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T10:34:05+00:00
Dialogue-key: 2b474b280d10
traces      : POL-INV-021

## Decision
Accept option A. IAS 2 ¶25 permits FIFO or weighted average and bars LIFO. IFRS for SMEs Section 13 is the same. Specific identification (IAS 2 ¶23) applies only to non-interchangeable items, and serial units of one Variant are interchangeable. Tenant-wide average avoids revaluing on every transfer, which a per-warehouse average (option C) would force. All standards references are *(not live-verified)*. PUR supplies unit cost on the receipt call, and each stock move carries cost for ACC and SLS. It binds POL-INV-021.
traces: POL-INV-021

Source      : governance-shared\analysis\modules\INV\_state\briefs\P0-round2.response2.md
