# Add G22 — F8 namespace violation

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: INV        Version: v1
Written: 2026-10-31T11:33:08+00:00

The plan puts the module under `com.erp.inv` (CORE layout, `crossmodule`, `integration.cat` adapters). `com.erp.*` belongs to the core, so this is forbidden item F8. The same defect was already raised and fixed in PTY (`revise-analysis.md`: `com.erp.pty` renamed to `com.commerce.pty`, with a platform finding for CAT). That makes it a known rule-level defect, so it is a MAJOR checklist violation here too.
