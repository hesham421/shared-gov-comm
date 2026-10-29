# G1 stays MAJOR and becomes more specific

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-29T05:37:06+00:00

On API-CAT-017 the failure is certain, not just possible. Hibernate always flushes inserts before updates, whatever order the service calls them in. So when a PUT drops a line and adds back the same componentVariantFk without a bundleComponentPk, the insert reaches the database before the deactivation. It hits UQ_CAT_BUNDLE_COMP_VARIANT, and a valid request is refused as RULE-CAT-019. API-CAT-019 and API-CAT-020 have the same exposure on UQ_CAT_ITEM_MEDIA_PRIMARY when updates are reordered. The fix is one rule at the level of ADR-CAT-047 and PHASE CORE: save and flush the row that clears the unique value before saving the row that sets it.
