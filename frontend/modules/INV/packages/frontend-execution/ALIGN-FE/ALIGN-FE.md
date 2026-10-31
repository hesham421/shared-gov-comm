<!-- source: PHASE:ALIGN-FE -->
<!-- traces: API-INV-001, API-INV-002, API-INV-003, API-INV-004, API-INV-005, API-INV-006, API-INV-007, API-INV-008, API-INV-009, API-INV-010, API-INV-011, API-INV-012, API-INV-013, API-INV-014, API-INV-015, API-INV-016, API-INV-017, API-INV-018, API-INV-019, API-INV-020, API-INV-021, API-INV-022, API-INV-023, API-INV-024, API-INV-025, API-INV-026, API-INV-027, API-INV-028, API-INV-029, API-INV-030, API-INV-031, API-INV-032, API-INV-033, API-INV-034, API-INV-035, API-INV-036, API-INV-037, API-INV-038, API-INV-039, API-INV-040, API-INV-041, API-INV-042, API-INV-043, API-INV-044, API-INV-045, API-INV-046, API-INV-047, API-INV-048, API-INV-049, API-INV-050, API-INV-051, API-INV-052, SCR-INV-001, SCR-INV-002, SCR-INV-003, SCR-INV-004, SCR-INV-005, SCR-INV-006, SCR-INV-007, SCR-INV-008, SCR-INV-009, SCR-INV-010, SCR-INV-011, SCR-INV-012, UXD-INV-001, UXD-INV-002, UXD-INV-003, UXD-INV-004, UXD-INV-005, UXD-INV-006, UXD-INV-007, UXD-INV-008, UXD-INV-009, UXD-INV-010, UXD-INV-011 -->
<!-- PHASE:ALIGN-FE:START traces=SCR-INV-001,SCR-INV-002,SCR-INV-003,SCR-INV-004,SCR-INV-005,SCR-INV-006,SCR-INV-007,SCR-INV-008,SCR-INV-009,SCR-INV-010,SCR-INV-011,SCR-INV-012,UXD-INV-001,UXD-INV-002,UXD-INV-003,UXD-INV-004,UXD-INV-005,UXD-INV-006,UXD-INV-007,UXD-INV-008,UXD-INV-009,UXD-INV-010,UXD-INV-011,API-INV-001,API-INV-002,API-INV-003,API-INV-004,API-INV-005,API-INV-006,API-INV-007,API-INV-008,API-INV-009,API-INV-010,API-INV-011,API-INV-012,API-INV-013,API-INV-014,API-INV-015,API-INV-016,API-INV-017,API-INV-018,API-INV-019,API-INV-020,API-INV-021,API-INV-022,API-INV-023,API-INV-024,API-INV-025,API-INV-026,API-INV-027,API-INV-028,API-INV-029,API-INV-030,API-INV-031,API-INV-032,API-INV-033,API-INV-034,API-INV-035,API-INV-036,API-INV-037,API-INV-038,API-INV-039,API-INV-040,API-INV-041,API-INV-042,API-INV-043,API-INV-044,API-INV-045,API-INV-046,API-INV-047,API-INV-048,API-INV-049,API-INV-050,API-INV-051,API-INV-052 -->
## ALIGN-FE

Each row names the check that backs it. Its mark is the analyze report's result for that check, copied in by
the orchestrator. A clause the report says examined nothing is written `— examined nothing`.

```
ALIGN — INV v1
row           backing check       assertion                                                                                         mark
SCREENS       orphans             every SCR is referenced by a plan block                                                           (stamped from the analyze report)
COMPOSITION   screen-composition  every SCR names where its secondary detail sits and that it saves once                            (stamped from the analyze report)
CONTAINER     composition-rule    every SCR names its container, and a child collection sits where that container puts it           (stamped from the analyze report)
READS         ux-reads-spec       every read a screen binds is an operation of api-spec-inv.yaml, and the plan names the document its mock server serves   (stamped from the analyze report)
UXD           orphans             every UXD is cited by a plan block — this is where a UX decision closes                           (stamped from the analyze report)
TRACES        traces              every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD          (stamped from the analyze report)
API           traces              every API this plan cites is an operation of api-spec-inv.yaml — never a line of the backend plan's prose   (stamped from the analyze report)
FOREIGN       xref-surface        every reference to another module's surface resolves in that module's own artifacts               (stamped from the analyze report)
REGISTRY      registry-agree      every UXD and SCR defined here is in the stage registry, and nothing else is                      (stamped from the analyze report)
LANGUAGES     languages           labels and messages in ar + en                                                                    (stamped from the analyze report)
MARKERS       markers             the parser reports no structural or semantic error for this track and plan                       (stamped from the analyze report)
DECISIONS     refs-exist          every ADR this plan cites exists on disk in analysis/decisions/INV/                              (stamped from the analyze report)
COVERAGE      (the report)        the clauses the analyze report lists as having examined nothing — verbatim, or `none`            (stamped from the analyze report)
```

```yaml name=self-check
findings: 0
clean: true
```

**Operations coverage** (operation │ API │ SCR action │ route │ status):

| Operation | API | SCR action | Route | Status |
|---|---|---|---|---|
| search warehouses | API-INV-001 | SCR-INV-001 search; warehouse lookup on SCR-INV-003 … 008, 010 | `/inventory/warehouses` | ✓ |
| read warehouse | API-INV-002 | SCR-INV-001 entry (edit); label fallback | `/inventory/warehouses?entry=<id>` | ✓ |
| create warehouse | API-INV-003 | SCR-INV-001 Save (new) | `/inventory/warehouses?entry=new` | ✓ |
| update warehouse | API-INV-004 | SCR-INV-001 Save (edit) | `/inventory/warehouses?entry=<id>` | ✓ |
| deactivate warehouse | API-INV-005 | SCR-INV-001 row Deactivate | `/inventory/warehouses` | ✓ |
| reactivate warehouse | API-INV-006 | SCR-INV-001 row Reactivate | `/inventory/warehouses` | ✓ |
| set as default warehouse | API-INV-007 | SCR-INV-001 row Set as default | `/inventory/warehouses` | ✓ |
| read inventory context | API-INV-008 | every SCR init; menu | `/inventory/*` | ✓ |
| search branches | API-INV-009 | SCR-INV-002 search; branch lookup on SCR-INV-001 | `/inventory/branches` | ✓ |
| read branch | API-INV-010 | SCR-INV-002 entry (edit) | `/inventory/branches?entry=<id>` | ✓ |
| create branch | API-INV-011 | SCR-INV-002 Save (new) | `/inventory/branches?entry=new` | ✓ |
| update branch | API-INV-012 | SCR-INV-002 Save (edit) | `/inventory/branches?entry=<id>` | ✓ |
| deactivate branch | API-INV-013 | SCR-INV-002 row Deactivate | `/inventory/branches` | ✓ |
| reactivate branch | API-INV-014 | SCR-INV-002 row Reactivate | `/inventory/branches` | ✓ |
| set as default branch | API-INV-015 | SCR-INV-002 row Set as default | `/inventory/branches` | ✓ |
| search stock balances | API-INV-016 | SCR-INV-003 search; SCR-INV-007 write-off seed | `/inventory/stock-balances` | ✓ |
| read availability of a Variant | API-INV-017 | SCR-INV-003 availability summary | `/inventory/stock-balances?variantPk=<id>` | ✓ |
| search stock moves | API-INV-018 | SCR-INV-004 search | `/inventory/stock-moves` | ✓ |
| read stock move | API-INV-019 | SCR-INV-004 move detail | `/inventory/stock-moves?move=<id>` | ✓ |
| search transfers | API-INV-020 | SCR-INV-005 search | `/inventory/transfers` | ✓ |
| read transfer | API-INV-021 | SCR-INV-005 entry | `/inventory/transfers/:id` | ✓ |
| create transfer | API-INV-022 | SCR-INV-005 Save (new) | `/inventory/transfers/new` | ✓ |
| update transfer | API-INV-023 | SCR-INV-005 Save (edit) | `/inventory/transfers/:id` | ✓ |
| post transfer | API-INV-024 | SCR-INV-005 Post | `/inventory/transfers/:id` | ✓ |
| cancel transfer | API-INV-025 | SCR-INV-005 Cancel | `/inventory/transfers/:id` | ✓ |
| search stock counts | API-INV-026 | SCR-INV-006 search | `/inventory/stock-counts` | ✓ |
| read stock count | API-INV-027 | SCR-INV-006 entry | `/inventory/stock-counts/:id` | ✓ |
| create stock count | API-INV-028 | SCR-INV-006 Start count | `/inventory/stock-counts/new` | ✓ |
| add counted quantity | API-INV-029 | SCR-INV-006 scan / add line | `/inventory/stock-counts/:id` | ✓ |
| change counted line | API-INV-030 | SCR-INV-006 line drawer confirm | `/inventory/stock-counts/:id?line=<lineId>` | ✓ |
| remove counted line | API-INV-031 | SCR-INV-006 line drawer remove | `/inventory/stock-counts/:id?line=<lineId>` | ✓ |
| post stock count | API-INV-032 | SCR-INV-006 Post | `/inventory/stock-counts/:id` | ✓ |
| cancel stock count | API-INV-033 | SCR-INV-006 Cancel | `/inventory/stock-counts/:id` | ✓ |
| search stock adjustments | API-INV-034 | SCR-INV-007 search | `/inventory/stock-adjustments` | ✓ |
| read stock adjustment | API-INV-035 | SCR-INV-007 entry | `/inventory/stock-adjustments/:id` | ✓ |
| create stock adjustment | API-INV-036 | SCR-INV-007 Save (new) | `/inventory/stock-adjustments/new` | ✓ |
| update stock adjustment | API-INV-037 | SCR-INV-007 Save (edit) | `/inventory/stock-adjustments/:id` | ✓ |
| post stock adjustment | API-INV-038 | SCR-INV-007 Post | `/inventory/stock-adjustments/:id` | ✓ |
| cancel stock adjustment | API-INV-039 | SCR-INV-007 Cancel | `/inventory/stock-adjustments/:id` | ✓ |
| search lots | API-INV-040 | SCR-INV-008 search / near-expiry view; lot lookup on SCR-INV-003 … 007 | `/inventory/lots` | ✓ |
| read lot | API-INV-041 | SCR-INV-008 entry; SCR-INV-007 write-off seed | `/inventory/lots?entry=<id>` | ✓ |
| correct lot expiry | API-INV-042 | SCR-INV-008 Save expiry | `/inventory/lots?entry=<id>` | ✓ |
| search Item stock settings | API-INV-043 | SCR-INV-009 search | `/inventory/item-stock-settings` | ✓ |
| read Item stock settings | API-INV-044 | SCR-INV-009 entry; line tracking on SCR-INV-005 … 007 | `/inventory/item-stock-settings?entry=<itemPk>` | ✓ |
| save Item stock settings | API-INV-045 | SCR-INV-009 Save | `/inventory/item-stock-settings?entry=<itemPk>` | ✓ |
| download opening stock sheet | API-INV-046 | SCR-INV-010 Download sheet | `/inventory/opening-stock` | ✓ |
| post opening stock | API-INV-047 | SCR-INV-010 Post (one idempotency key per load — ADR-INV-053) | `/inventory/opening-stock` | ✓ |
| read inventory settings | API-INV-048 | SCR-INV-011 load | `/inventory/settings` | ✓ |
| update inventory settings | API-INV-049 | SCR-INV-011 Save | `/inventory/settings` | ✓ |
| search stock valuation | API-INV-050 | SCR-INV-012 search | `/inventory/stock-valuation` | ✓ |
| read stock valuation of a Variant | API-INV-051 | SCR-INV-012 Variant detail | `/inventory/stock-valuation?variant=<id>` | ✓ |
| read an INV lookup key | API-INV-052 | LOOKUP-INV_* hooks of SCR-INV-004 (move type, document type), SCR-INV-005 and SCR-INV-006 (document status), SCR-INV-007 (reason, document status) | `/inventory/*` | ✓ |

52 / 52 operations have a screen action and a route. No open item: the warehouse label for a Seller/Cashier on
SCR-INV-003 is read from the row (ADR-INV-036, closed at gate-analysis revise 1).
<!-- PHASE:ALIGN-FE:END -->
