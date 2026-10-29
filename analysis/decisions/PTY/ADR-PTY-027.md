# ADR-PTY-027 — Query strategy: Party join, native normalised search and matching

Status  : ACCEPTED (non-breaking) · Stage : P3.1 · Module : PTY v1 · Date : 2026-10-29

## Context
The engine's join governance says single-table responses do not join, and that any join or native query needs an ADR. Customer and Supplier hold no names and no code of their own: the SRS "presents" them from the Party, and the db-script stores them only on PTY_PARTY (ADR-PTY-014, REQ-PTY-024). The global search must compare without letter case and with Arabic letter forms unified (REQ-PTY-042). Guest matching compares normalised mobiles (ADR-PTY-015). The db-script installs no database extension.

## Decision
- **Party join.** Every read or search of a role joins PTY_PARTY on the role's PARTY_FK to return code, nameAr, nameEn, mobile, email and taxRegistrationNo. This applies to QR-PTY-005, 010, 012, 013, 015 and 020. Credit override reads and searches (QR-PTY-032, 035) join PTY_CUSTOMER and PTY_PARTY to show the Customer's code and names. All joins use intra-module FK paths. Lookup labels are never joined; the client resolves codes through the given mdl.
- **Global search (QR-PTY-011)** is a native query:
  - Arabic is unified with `TRANSLATE(x, 'أإآةى', 'اااهي')` on both sides, and the remaining text with `LOWER`.
  - Mobile is compared after `REGEXP_REPLACE(MOBILE, '[ -]', '', 'g')`.
  - Results are ordered by an exact code match first, then by the display name in the request language (falling back to the other language), and limited to 10.
- **Guest matching:**
  - Email (QR-PTY-036) is compared as `LOWER(BTRIM(EMAIL))`, the same expression as UQ_PTY_PARTY_EMAIL, so the index serves it.
  - Mobile (QR-PTY-037) is a native query that removes spaces and dashes and maps a leading "00" to "+". It reads `LIMIT 2`, because only "exactly one" versus "more than one" matters.
- No pg_trgm, unaccent or other extension is used. Search performance relies on the db-script's B-tree indexes and the bounded result size.

## Consequences
- For large tenants, "contains" searches scan the joined rows. If profiling demands it, a later version may add trigram indexes, which is a platform decision about extensions.
- The normalisation rules sit in one query each, and the in-process and REST paths share them.

## Traces
QR-PTY-005, 010, 011, 012, 013, 015, 020, 032, 035, 036, 037 · REQ-PTY-013, REQ-PTY-018, REQ-PTY-024, REQ-PTY-041, REQ-PTY-042, REQ-PTY-055, REQ-PTY-056, REQ-PTY-101 · ADR-PTY-014, ADR-PTY-015
