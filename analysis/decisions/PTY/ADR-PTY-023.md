# ADR-PTY-023 — ADR sequence continued after the db-script's recorded decisions

Status  : ACCEPTED (non-breaking) · Stage : P3.1 · Module : PTY v1 · Date : 2026-10-29

## Context
The P3.1 brief states that the next free ADR id is ADR-PTY-018, counted from the files present in `analysis/decisions/PTY/` (ADR-PTY-001 … ADR-PTY-017). The current db-script (P2) and its registry, however, already record five decisions under ADR-PTY-018 … ADR-PTY-022 (physical precision, audit principal columns, no currency column, tenant scoping without a tenant column, lookup seed against the given mdl tables) and state "Next free ADR : ADR-PTY-023". Those five were recorded in full inside the db-script for the orchestrator to file, and no file exists for them yet. Writing new decisions under ADR-PTY-018 … 022 would give two different decisions the same id.

## Decision
- This stage raises its decisions from ADR-PTY-023 onwards, so that no id already used by the db-script is reused.
- The plan applies the five P2 decisions exactly as the db-script states them, and cites them as "the db-script §5.2 decisions" rather than by id, because their files do not exist on disk.
- Filing ADR-PTY-018 … ADR-PTY-022 under `analysis/decisions/PTY/` from the db-script's §5.2 text is left to the orchestrator, which owns that step. This stage does not write those files, because it does not own them.

## Consequences
- ADR ids stay unique and continuous with the current state: ADR-PTY-023 … ADR-PTY-029 are raised here, and the next free id is ADR-PTY-030.
- Until the P2 files are filed, a reader of `analysis/decisions/PTY/` sees a gap at 018 … 022. The db-script remains the authoritative text of those five decisions.

## Traces
backend-execution-plan-pty.md (Decisions) · registry-exec-be-pty.md (ADRs) · db-script §5.2 · registry-db "Next free ADR"
