# ADR-PTY-030 — P2 decisions ADR-PTY-018 … ADR-PTY-022 filed from the db-script §5.2

Status  : ACCEPTED (non-breaking) · Stage : P3.1 (repair round 1) · Module : PTY v1 · Date : 2026-10-29

## Context
The db-script and registry-db cite ADR-PTY-018 … ADR-PTY-022, and they record those five decisions in full in db-script §5.2 "for the orchestrator to file". No file existed for them in `analysis/decisions/PTY/`, so `gov.py analyze` refused the stage (C7.13 refs-exist, 10 CRITICAL findings). ADR-PTY-023 had left the filing to the orchestrator. This stage cannot edit the db-script, so the citations cannot be removed from the side that makes them.

## Decision
- File ADR-PTY-018 … ADR-PTY-022 under `analysis/decisions/PTY/`. Each file copies the db-script §5.2 text word for word (Context · Decision · Consequences · Traces · Status) and names the P2 stage as its origin. No decision is changed or added.
- This does not rewrite an existing ADR: none of the five ids had a file. ADR-PTY-023 stays as it is. Its third bullet ("left to the orchestrator") is superseded by this ADR for those five files only.

## Consequences
- Every ADR the db-script, registry-db and this plan cite now exists on disk. The ADR stream 001 … 032 has no gap.
- The db-script remains the text these files were copied from. If a later P2 version amends one of these decisions, it is recorded as a new ADR, never by editing these files.

## Traces
db-script §5.2 · registry-db (Decisions) · ADR-PTY-023 · backend-execution-plan-pty.md (Decisions) · registry-exec-be-pty.md (ADRs)
