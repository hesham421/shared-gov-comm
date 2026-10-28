# ADR-CAT-050 — Display data served by the given: lookup values, capability flags, image files

Status : ACCEPTED (non-breaking) · Stage : P3.2 · Module : CAT v2 · Date : 2026-10-28
Amended by : ADR-CAT-065 — §1 and §2 no longer hold for UXD-CAT-001. The lookup values reach the screens through CAT's own read `GET /api/v1/cat/lookups/{key}` (API-CAT-052, `OwnedLookups`), not the shell's lookup client. UXD-CAT-002 and UXD-CAT-003 are unchanged.

## Context
CAT screens show three kinds of data that no operation of `api-spec-cat.yaml` serves:
- the values and labels of the CAT_* lookup keys, which are loaded at request time from the core lookup module (REQ-CAT-073, REQ-CAT-074, REQ-CAT-076; SRS A2 "Lookup values are loaded at runtime from `mdl`");
- the `variants` and `bundles` capability flags, which decide whether Variant and Bundle functions are shown (REQ-CAT-011, REQ-CAT-012, REQ-CAT-014, REQ-CAT-015; ADR-CAT-041);
- the image file behind `fileDocumentRef`, which the given `file` module keeps in public image storage (REQ-CAT-105, REQ-CAT-109; D-08).

CAT consumes no business module (`consumes: []`), so no XM edge exists or is needed. The screens still depend on a source outside CAT.

## Decision
1. Each of the three needs is a cross-module display dependency (UXD), minted by P3.2 and keyed to the screens that show it:
   - UXD-CAT-001: lookup values and labels (owner `erp-core:modules.mdl`);
   - UXD-CAT-002: capability flags (owner `erp-core:modules.cu` behind FeatureFlags; the frontend reads them from the platform's app configuration read `GET /api/public/app-config` [KB:raw-idea.md, technical base]);
   - UXD-CAT-003: image file rendering (owner `erp-core:modules.file`, public image storage).
2. The frontend reads them through the platform shell's clients for the given. It never reads them through a CAT operation, and it does not invent a CAT endpoint for them. Lookup hooks are of kind `lookup`, with one hook per key shared across screens and a long-lived cache. The flag read is of kind `init` and is refreshed on every screen entry and on window focus (REQ-CAT-012: a change applies from the next request).
3. When a flag cannot be read, the screen treats the flag-bound function as unavailable and shows RULE-CAT-009's text. This mirrors REQ-CAT-013 on the client side; the server stays authoritative.

## Consequences
- UXD-CAT-001 … 003 are recorded in the ui-ux-spec and the P3.2 registry, and they are cited by F-blocks. They share nothing with XM and do not appear in backend artifacts.
- The user's permission set (the `sec` module) is platform session context read by the route guards. It is not a UXD, because no field shows it.

## Traces
REQ-CAT-011 · REQ-CAT-012 · REQ-CAT-013 · REQ-CAT-014 · REQ-CAT-015 · REQ-CAT-073 · REQ-CAT-074 · REQ-CAT-076 · REQ-CAT-105 · REQ-CAT-109 · UXD-CAT-001 · UXD-CAT-002 · UXD-CAT-003
