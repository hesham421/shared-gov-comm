# Flow diagram — Party / الأطراف (PTY) v1
══════════════════════════════════════════════════════════════════
Stage   : P3.2 Part A — UX design · Module : PTY · Version : v1 · Profile : commerce
Inputs  : srs (SCR-REQ-PTY-001 … 005, A4, A8.1) · prd (US-PTY-001 … 017, priorities) · api-spec-pty.yaml
Screens : SCR-PTY-001 Customers · SCR-PTY-002 Suppliers · SCR-PTY-003 Customer credit · SCR-PTY-004 Credit override requests · SCR-PTY-005 Credit control settings
Flows   : 20 · every flow cites a US-* and an SRS-backed SCR-* (B1)
══════════════════════════════════════════════════════════════════

Notation: `→` is one navigation step; `[cmd]` is a command on the screen (a state change with its own confirmation, never a second save); `(flag)` marks a step that exists only while `b2bCredit` is on (REQ-PTY-070, REQ-PTY-073). Routes are the ones the ui-ux-spec fixes per screen.

## Customers

FLOW — Customer search and maintenance                                   traces=US-PTY-001,US-PTY-006,REQ-PTY-001,REQ-PTY-012,REQ-PTY-013,REQ-PTY-039,SCR-PTY-001
Screens   : SCR-PTY-001
Sequence  : menu Party → Customers → SCR-PTY-001 Search (/pty/customers, filters code · name · mobile · email · taxRegistrationNo · customerGroupCode · isActiveFl) → open a row → SCR-PTY-001 Entry (/pty/customers/:customerId) → edit names / contacts / customer group → Save → back to Search with the same filters
Trigger   : the Owner or Seller/Cashier needs a Customer of the one customer base
Priority  : HIGH (US-PTY-001) · — (US-PTY-006)

FLOW — Customer registration in the back-office                           traces=US-PTY-002,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,SCR-PTY-001
Screens   : SCR-PTY-001
Sequence  : SCR-PTY-001 Search → New customer → SCR-PTY-001 Entry (/pty/customers/new; customer group prefilled RETAIL) → enter a name (Arabic or English) → Save → SCR-PTY-001 Entry (/pty/customers/:customerId, code assigned) → exit
Trigger   : a counterparty starts buying from the trader
Priority  : HIGH

FLOW — Customer registration in place from a sale                         traces=US-PTY-002,REQ-PTY-005,REQ-PTY-008,AC-PTY-007,AC-PTY-010,SCR-PTY-001
Screens   : SCR-PTY-001
Sequence  : sale screen (Sales & POS) → "New customer" → SCR-PTY-001 Quick register (in place, mounted by the sale; one mandatory mark: the name) → Save → the sale receives the Customer identifier and code and stays open with that Customer selected
Trigger   : the Seller/Cashier sells to a customer who is not yet registered
Priority  : HIGH

FLOW — Customer addresses                                                 traces=US-PTY-005,REQ-PTY-030,REQ-PTY-031,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,SCR-PTY-001
Screens   : SCR-PTY-001
Sequence  : SCR-PTY-001 Entry → Addresses pane → Add address / open an address → Address line drawer (/pty/customers/:customerId/addresses/new | /:addressKey) → Confirm (returns the line to the page) → mark default / remove a line in the pane → the page's one Save → Entry shows the saved addresses and the default of each type
Trigger   : a Customer gives a billing or delivery address
Priority  : HIGH

FLOW — Customer deactivation and reactivation                             traces=US-PTY-001,REQ-PTY-014,REQ-PTY-015,REQ-PTY-023,REQ-PTY-028,REQ-PTY-029,SCR-PTY-001
Screens   : SCR-PTY-001
Sequence  : SCR-PTY-001 Entry → [cmd] Deactivate → confirmation → message of RULE-PTY-006 → Entry shows inactive · later: Search (isActiveFl = no) → Entry → [cmd] Reactivate → Entry shows active
Trigger   : the Owner stops or resumes dealing with a Customer
Priority  : HIGH

FLOW — Walk-in Customer maintenance                                       traces=US-PTY-008,REQ-PTY-047,REQ-PTY-049,AC-PTY-055,AC-PTY-057,SCR-PTY-001
Screens   : SCR-PTY-001
Sequence  : SCR-PTY-001 Search (walk-in Customer marked) → Entry → rename → Save · [cmd] Deactivate → refused with the message of RULE-PTY-010 · [cmd] Make supplier → refused with the message of RULE-PTY-012 → Entry unchanged
Trigger   : the Owner renames the walk-in Customer (D-08) or tries to retire it
Priority  : HIGH

FLOW — Make a Customer also a Supplier                                    traces=US-PTY-004,REQ-PTY-024,REQ-PTY-025,REQ-PTY-027,SCR-PTY-001,SCR-PTY-002
Screens   : SCR-PTY-001, SCR-PTY-002
Sequence  : SCR-PTY-001 Entry → [cmd] Make supplier «إضافة كمورّد» → confirmation → SCR-PTY-002 Entry (/pty/suppliers/:supplierPk of the created Supplier) → exit
Trigger   : the Owner starts buying from a Customer
Priority  : HIGH

FLOW — Global search to a Customer                                        traces=US-PTY-007,REQ-PTY-041,REQ-PTY-042,REQ-PTY-043,SCR-PTY-001
Screens   : SCR-PTY-001
Sequence  : application global search (shell) → customer result → SCR-PTY-001 Entry (/pty/customers/:customerId) → exit
Trigger   : the Seller/Cashier knows something about a Customer but not which screen holds it
Priority  : HIGH

## Suppliers

FLOW — Supplier search, registration and maintenance                      traces=US-PTY-003,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-022,SCR-PTY-002
Screens   : SCR-PTY-002
Sequence  : menu Party → Suppliers → SCR-PTY-002 Search (/pty/suppliers) → New supplier (/pty/suppliers/new) or open a row (/pty/suppliers/:supplierId) → names / contacts / payment terms / addresses → Save → exit
Trigger   : the Owner registers or updates a counterparty the trader buys from; a Warehouse user reads one
Priority  : HIGH

FLOW — Supplier registration in place from a purchase document           traces=US-PTY-003,REQ-PTY-008,REQ-PTY-016,AC-PTY-011,AC-PTY-021,SCR-PTY-002
Screens   : SCR-PTY-002
Sequence  : purchase document (Purchasing) → "New supplier" → SCR-PTY-002 Quick register (in place, mounted by the document; one mandatory mark: the name) → Save → the document receives the Supplier identifier and code
Trigger   : the Owner records a purchase from a supplier not yet registered
Priority  : HIGH

FLOW — Supplier deactivation and reactivation                             traces=US-PTY-003,REQ-PTY-020,REQ-PTY-021,REQ-PTY-023,SCR-PTY-002
Screens   : SCR-PTY-002
Sequence  : SCR-PTY-002 Entry → [cmd] Deactivate → confirmation → message of RULE-PTY-006 · later: Search (isActiveFl = no) → Entry → [cmd] Reactivate
Trigger   : the Owner stops or resumes buying from a Supplier
Priority  : HIGH

FLOW — Make a Supplier also a Customer                                    traces=US-PTY-004,REQ-PTY-024,REQ-PTY-026,REQ-PTY-027,SCR-PTY-002,SCR-PTY-001
Screens   : SCR-PTY-002, SCR-PTY-001
Sequence  : SCR-PTY-002 Entry → [cmd] Make customer «إضافة كعميل» → confirmation → SCR-PTY-001 Entry (/pty/customers/:customerPk of the created Customer, customer group RETAIL) → exit
Trigger   : the Owner starts selling to a Supplier
Priority  : HIGH

## Credit (all steps exist only while `b2bCredit` is on)

FLOW — Set a Customer's credit limit and payment terms                    traces=US-PTY-011,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-048,SCR-PTY-001,SCR-PTY-003
Screens   : SCR-PTY-001, SCR-PTY-003
Sequence  : (flag) menu Party → Credit → Customer credit → SCR-PTY-003 Search (/pty/customer-credits) → open a row, or SCR-PTY-001 Entry → "Edit credit" → SCR-PTY-003 Entry (/pty/customer-credits/:customerId) → credit limit · payment terms → Save → exit
Trigger   : the Owner agrees credit terms with a Customer
Priority  : HIGH

FLOW — Credit appears and disappears with the flag                        traces=US-PTY-012,REQ-PTY-070,REQ-PTY-071,REQ-PTY-073,AC-PTY-078,AC-PTY-082,SCR-PTY-001,SCR-PTY-003,SCR-PTY-004,SCR-PTY-005
Screens   : SCR-PTY-001, SCR-PTY-003, SCR-PTY-004, SCR-PTY-005
Sequence  : any screen load → application configuration read → flag off: menu shows Customers and Suppliers only; SCR-PTY-001 shows no credit field · flag on (next screen load): menu adds Customer credit, Credit override requests, Credit control settings; SCR-PTY-001 shows the read-only credit values → no setup step
Trigger   : the business turns `b2bCredit` on or off
Priority  : HIGH

FLOW — Clear credit after a refused switch-off                            traces=US-PTY-013,REQ-PTY-076,REQ-PTY-077,REQ-PTY-078,AC-PTY-087,AC-PTY-088,SCR-PTY-003
Screens   : SCR-PTY-003
Sequence  : (flag) the flag owner's refusal (message of RULE-PTY-024) → "Show customers with credit" «عرض العملاء ذوي الائتمان» → SCR-PTY-003 Search (/pty/customer-credits?carriesCredit=true) → open a row → [cmd] Clear credit «مسح الائتمان» → back to the filtered Search → exit
Trigger   : the Owner tries to switch `b2bCredit` off while Customers carry credit
Priority  : HIGH

FLOW — Credit standing of a Customer                                      traces=US-PTY-014,REQ-PTY-068,REQ-PTY-080,REQ-PTY-085,AC-PTY-096,SCR-PTY-003
Screens   : SCR-PTY-003
Sequence  : (flag) SCR-PTY-003 Search → open a row → SCR-PTY-003 Entry shows credit limit · open exposure · available credit → exit
Trigger   : the Seller/Cashier or the Owner checks a Customer before agreeing a credit sale
Priority  : HIGH

FLOW — Choose the credit-overrun action                                   traces=US-PTY-015,REQ-PTY-086,REQ-PTY-087,REQ-PTY-090,SCR-PTY-003,SCR-PTY-005
Screens   : SCR-PTY-003, SCR-PTY-005
Sequence  : (flag) menu Party → Credit → Credit control settings, or SCR-PTY-003 → "Credit control settings" → SCR-PTY-005 (/pty/credit-settings; BLOCK_WITH_OVERRIDE preselected when nothing is saved) → choose WARN or BLOCK_WITH_OVERRIDE → Save → exit
Trigger   : the Owner decides how strict credit control is
Priority  : HIGH

FLOW — Request approval for a credit sale above the limit                traces=US-PTY-016,REQ-PTY-089,REQ-PTY-091,REQ-PTY-092,REQ-PTY-104,REQ-PTY-105,AC-PTY-102,SCR-PTY-004
Screens   : SCR-PTY-004
Sequence  : (flag) sale screen whose credit check requires approval → SCR-PTY-004 New request (/pty/credit-overrides/new?customerId=…&saleAmount=…&sourceDocumentRef=…, prefilled) → "Request approval" «طلب موافقة» → SCR-PTY-004 Detail (/pty/credit-overrides/:requestId, status PENDING, number assigned) → exit
Trigger   : the Seller/Cashier is blocked by BLOCK_WITH_OVERRIDE on a credit sale
Priority  : HIGH

FLOW — Approver decides a request                                         traces=US-PTY-016,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,SCR-PTY-004,SCR-PTY-003
Screens   : SCR-PTY-004, SCR-PTY-003
Sequence  : (flag) in-app notification, or menu Party → Credit → Credit override requests → SCR-PTY-004 Search (PENDING first) → SCR-PTY-004 Detail (/pty/credit-overrides/:requestId) → optionally SCR-PTY-003 Entry (/pty/customer-credits/:customerFk) and back → decision panel: [cmd] Approve «موافقة» or reason + [cmd] Reject «رفض» → Detail shows the decision → exit
Trigger   : a request waits for a user holding UPDATE on PTY_CREDIT_OVERRIDE
Priority  : HIGH

FLOW — Requester follows or cancels own requests                         traces=US-PTY-016,REQ-PTY-096,REQ-PTY-102,AC-PTY-109,AC-PTY-116,SCR-PTY-004
Screens   : SCR-PTY-004
Sequence  : (flag) menu Party → Credit → Credit override requests → SCR-PTY-004 Search (own requests only) → Detail → [cmd] Cancel request «إلغاء الطلب» (PENDING only) → Detail shows CANCELLED → exit
Trigger   : the sale was settled another way, or the Seller/Cashier checks the outcome
Priority  : HIGH

FLOW — Credit history of a Customer                                       traces=US-PTY-017,REQ-PTY-106,REQ-PTY-108,AC-PTY-122,SCR-PTY-003
Screens   : SCR-PTY-003
Sequence  : (flag) SCR-PTY-003 Entry → Credit history pane «سجل الائتمان» (newest first: field, old value, new value, user, time) → exit
Trigger   : the Owner needs to know who changed credit terms and when
Priority  : —

## Stories not used in a flow

| US | Reason (B1) |
|---|---|
| US-PTY-009 | Guest checkout happens in the storefront (Storefront module); the SRS gives it no PTY screen — it is the operation `POST /api/v1/pty/guest-customers` offered to consuming modules (SRS A8.1). No navigation is invented here. |
| US-PTY-010 | Same as US-PTY-009 — matching and invitation are API behaviour (A8.1); no PTY screen. |

Every other story (US-PTY-001 … 008, 011 … 017) appears in at least one flow above.
