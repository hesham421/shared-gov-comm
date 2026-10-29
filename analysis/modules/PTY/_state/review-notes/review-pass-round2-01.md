# Round-1 findings G1–G11 stand

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: PTY        Version: v1
Written: 2026-10-29T08:52:40+00:00

Every round-1 finding was re-checked against the artifacts and the installed checklist (`.claude/skills/erp-core-usage@1.6.0/templates/checklist.md`). G3 is confirmed word for word against C6, and G4 against C13. G2 stays MAJOR, but its rationale is corrected: checklist C4 does allow `TenantContext.runAs`. The defect is the divergence from the upstream SRS A9 and ADR-PTY-044, not a checklist breach.
