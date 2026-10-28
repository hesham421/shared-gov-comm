# Add G16 and G17 as MAJOR

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-28T10:14:15+00:00

The plan uses the package root com.erp.cat, which breaks checklist line F8. It declares no @Audited entities although the registry depends on audit for change history, which breaks line C16.
