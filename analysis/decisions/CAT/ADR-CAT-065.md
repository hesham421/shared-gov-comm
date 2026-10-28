# ADR-CAT-065 — CAT serves its own lookup keys through OwnedLookups

Status : ACCEPTED (non-breaking) · Stage : P1 / P3.1 / P3.2 (gate-analysis round 2, G5) · Module : CAT v2 · Date : 2026-10-28
Amends : ADR-CAT-050 §1 and §2 (for UXD-CAT-001 only; UXD-CAT-002 and UXD-CAT-003 are unchanged)

## Context
erp-core requires the owning module to serve its own keys to the UI through `OwnedLookups`, never through a pass-through:
- `mdl.md`: "serves its own keys to the UI through `OwnedLookups` (never a pass-through for arbitrary keys)";
- conventions § Lookups, rule 4;
- checklist C6.

CAT owns 10 keys (SRS A6), but no CAT operation served them. ADR-CAT-050 and UXD-CAT-001 had the screens read them through "the given's lookup client". That is the pass-through the core forbids, and the backend plan named no `OwnedLookups`.

## Decision
1. **New API-CAT-052, `GET /api/v1/cat/lookups/{key}`.** `CatLookupService.read(key)` returns `OwnedLookups.read(key, CAT_LOOKUP_KEYS, CatErrorCodes.LOOKUP_KEY_UNKNOWN, mdlLookupApi::readActiveValuesByKey)`, mapped to `LookupOption {code, labelAr, labelEn, sortOrder}`. `CAT_LOOKUP_KEYS` holds the 10 keys of SRS A6.
2. **Unknown keys.** Any other key, or a key mdl answers 404 for, answers `CAT-404-LOOKUP-KEY-UNKNOWN`. This is a PLATFORM-STD Error Catalog row with an ar and en message.
3. **Permission.** The endpoint is guarded by `hasAnyAuthority` on the VIEW permission of any CAT page, because every CAT screen shows lookup-backed fields. It is listed in the permission matrix under SCR-REQ-CAT-001 and SCR-REQ-CAT-006.
4. **Given usage.** A `given-usage` row records the owned-lookups use. erp-core's manifest lists `owned-lookups` under `common` but publishes no interface or API document for it. C-GIVEN-2 accepts only a published `via`, so the row names the mdl interface the call goes through (`MdlLookupApi`) and names `OwnedLookups` in its purpose. The manifest gap is recorded as an OPEN platform finding.
5. **Frontend.** UXD-CAT-001 and every `*-LOOKUP` hook bind to API-CAT-052. **This amends ADR-CAT-050 §1–§2:** the lookup values are still owned by the given's data, but they reach the screen through CAT's own read and not the shell's lookup client. Their cache policy is unchanged (staleTime 30 min).

## Consequences
- API 51 → 52 and catalog 58 → 60 rows (with RULE-CAT-053 of ADR-CAT-063). The SRS B5 table of SCR-REQ-CAT-001 lists the read.
- The given-usage block has 11 rows, in the SRS and the contract alike.
- New TC-CAT-207 (backend). Frontend TC-CAT-137, 138, 139, 177 and 178 name the endpoint.

## Traces
REQ-CAT-073 · REQ-CAT-074 · REQ-CAT-076 · UXD-CAT-001 · API-CAT-052 · ADR-CAT-050
