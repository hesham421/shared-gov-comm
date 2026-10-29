I fixed all 13 C3.7 findings. I edited only `analysis/modules/INV/P0/module-registry-inv.md`. I didn't rerun `gov.py analyze` because the orchestrator runs it, so whether the stage now passes isn't confirmed yet.

**What was wrong:** the INV module registry only named POL-INV-014 to 022, inside its decision text. POL-INV-001 to 013 were defined in the business policies but never appeared in the registry.

**What I changed:**
- **New `POLICIES REGISTERED` section** (after DEPENDENCIES): it lists all 22 policies, POL-INV-001 to 022. Each row has its name in English and Arabic, its EARS pattern and its status. It is only an index; the policy statements still live only in `business-policies-inv.md`. I copied this layout from the CAT v2 registry (`analysis/modules/CAT/v2/P0/module-registry-cat.md:68`), which already passes C3.7.
- **One new `AUTO:` line** under AUTO-DECISIONS: it records that the registry indexes every policy, citing engine §4, C3.7 and the CAT layout.

**Not changed:**
- `platform-summary.md` and `business-policies-inv.md` are untouched.
- No ids were renumbered and no `[QUESTION]` was added.
- No new ADR: the fix copies a layout that already passes, so there was no choice to record and ADR-INV-011 stays unused.