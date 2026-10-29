## BUSINESS POLICIES — Party / الأطراف (PTY)
══════════════════════════════════════════════════════════════════
Module   : PTY     Source of truth : user vision text + dialogue resolutions
Read by  : P0.5 (every user story cites the policies it serves)
══════════════════════════════════════════════════════════════════

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers)

POL-PTY-001 — One customer base / قاعدة عملاء واحدة
  Statement    : The system shall keep one customer base that the back-office, the point of sale and the storefront share.
  Statement-ar : يجب أن يحتفظ النظام بقاعدة عملاء واحدة تتشاركها الأعمال الخلفية ونقطة البيع والمتجر الإلكتروني.
  Pattern   : ubiquitous
  Trigger   : Create / Update
  Rationale : the product's differentiator is one catalogue, one stock and one customer base across channels.
  Source    : KB:raw-idea.md §1 "one catalogue, one stock, one customer base"
  Status    : CONFIRMED

POL-PTY-002 — Guest becomes a customer after the first order / تحويل الزائر إلى عميل بعد أول طلب
  Statement    : When a guest completes their first storefront order, the system shall register the guest as a Customer linked to a CUSTOMER-realm account.
  Statement-ar : عندما يُتمّ الزائر أول طلب له في المتجر الإلكتروني، يجب أن يسجّل النظامُ الزائرَ عميلاً مرتبطاً بحساب في نطاق CUSTOMER.
  Pattern   : event
  Trigger   : Submit (first storefront order)
  Rationale : guest checkout removes friction, and the shopper still joins the one customer base.
  Source    : KB:raw-idea.md §3 D5 "the guest becomes a CUSTOMER-realm account after the first order"; DP §5 G9; dialogue resolution 5 (matching contact is linked by invitation)
  Status    : CONFIRMED

POL-PTY-003 — Credit for wholesale customers / الائتمان لعملاء الجملة
  Statement    : Where the capability flag `b2bCredit` is on, the system shall let the trader set a credit limit and payment terms for each customer.
  Statement-ar : حيثما كان مفتاح القدرة `b2bCredit` مفعّلاً، يجب أن يتيح النظام للتاجر تحديد حد ائتمان وشروط دفع لكل عميل.
  Pattern   : optional
  Trigger   : Create / Update (Customer)
  Rationale : wholesale customers buy on credit within limits and terms.
  Source    : KB:raw-idea.md §4.1 "`b2bCredit` — wholesale customers: credit limits, terms, price lists"
  Status    : CONFIRMED

POL-PTY-004 — Credit hidden while the flag is off / إخفاء الائتمان عند تعطيل المفتاح
  Statement    : While the capability flag `b2bCredit` is off, the system shall hide customer credit limits and customer payment terms.
  Statement-ar : طالما كان مفتاح القدرة `b2bCredit` معطّلاً، يجب أن يُخفي النظام حدود ائتمان العملاء وشروط دفعهم.
  Pattern   : state
  Trigger   : Display
  Rationale : only what is enabled is shown; no dead menus or fields.
  Source    : KB:raw-idea.md §4.1 "the UI shows fields/menus/reports only when the flag is on", §6.3
  Status    : CONFIRMED

POL-PTY-005 — Credit flag cannot be turned off while used / منع تعطيل مفتاح الائتمان أثناء استخدامه
  Statement    : If a user turns the capability flag `b2bCredit` off when any customer has a credit limit or payment terms, then the system shall refuse the change.
  Statement-ar : إذا حاول مستخدم تعطيل مفتاح القدرة `b2bCredit` عندما يكون لأي عميل حد ائتمان أو شروط دفع، فيجب أن يرفض النظام التغيير.
  Pattern   : unwanted
  Trigger   : Update (capability flag)
  Rationale : a flag may be turned on at any time; turning it off is blocked while data depends on it.
  Source    : KB:raw-idea.md §6.2 "turning off is blocked when data depends on it"; DP §5 G3
  Status    : CONFIRMED

POL-PTY-006 — Quick party registration / تسجيل سريع للأطراف
  Statement    : The system shall let a user register a Customer or a Supplier with no more than four mandatory inputs.
  Statement-ar : يجب أن يتيح النظام للمستخدم تسجيل عميل أو مورّد بما لا يزيد على أربعة مدخلات إلزامية.
  Pattern   : ubiquitous
  Trigger   : Create
  Rationale : simplicity is measurable: at most four mandatory fields per screen, and setup plus the first invoice in 30 minutes or less.
  Source    : KB:raw-idea.md §3 D9, §6.1 "≤ 4 mandatory fields per screen", §6.10; DP §5 G7
  Status    : CONFIRMED

POL-PTY-007 — Customers in global search / العملاء في البحث الشامل
  Statement    : The system shall make every Customer findable through the one global search.
  Statement-ar : يجب أن يجعل النظام كل عميل قابلاً للعثور عليه عبر البحث الشامل الموحّد.
  Pattern   : ubiquitous
  Trigger   : Search
  Rationale : one global search covers items, customers and documents.
  Source    : KB:raw-idea.md §6.6 "One global search (items, customers, documents)"
  Status    : CONFIRMED

POL-PTY-008 — Credit overrun handling / معالجة تجاوز حد الائتمان
  Statement    : If a sale would take a customer's open exposure above the customer's credit limit, then the system shall apply the tenant's credit-overrun setting, which by default blocks the sale unless a user holding the credit-override permission approves it.
  Statement-ar : إذا كان من شأن عملية بيع أن ترفع انكشاف العميل المفتوح فوق حد ائتمانه، فيجب أن يطبّق النظام إعداد المستأجر لتجاوز الائتمان، الذي يمنع البيع افتراضياً ما لم يوافق عليه مستخدم يملك صلاحية تجاوز الائتمان.
  Pattern   : unwanted
  Trigger   : Submit (sales document)
  Rationale : wholesale credit needs a control, yet nothing is imposed: the tenant may choose to warn only, and a permitted role always has a next action.
  Source    : dialogue resolution 7; KB:raw-idea.md §6.2 "nothing imposed", §6.5 "every error offers a next action", §3 D8 (role presets), §4.1 (`b2bCredit`)
  Status    : CONFIRMED

POL-PTY-009 — Walk-in customer for unnamed POS sales / العميل النقدي لمبيعات نقطة البيع بلا عميل مسمّى
  Statement    : When a cashier completes a point-of-sale sale without naming a customer, the system shall record the sale against the tenant's walk-in customer.
  Statement-ar : عندما يُتمّ الكاشير عملية بيع في نقطة البيع دون تحديد عميل، يجب أن يسجّل النظام البيع على العميل النقدي الخاص بالمستأجر.
  Pattern   : event
  Trigger   : Submit (point-of-sale sale)
  Rationale : a new employee sells within ten minutes, and every invoice still has a party for ageing and reports.
  Source    : dialogue resolution 9; KB:raw-idea.md §6.8 (defaults), §6.10 "a new employee sells after ≤ 10 minutes"
  Status    : CONFIRMED

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
None — standard values apply.

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
|---|---|---|---|
| Marketplace sellers / البائعون في سوق متعدد البائعين | A Supplier never sells through the storefront; the storefront has one seller, the tenant | a later version that adds a marketplace | KB:raw-idea.md §9 |
| Multi-currency party balances / أرصدة الأطراف متعددة العملات | Parties trade in the tenant's single default currency in v1; the currency dimension exists in the model, hidden while its count is 1 | wave 4 "advanced" (multi-currency) | KB:raw-idea.md §6.8, §8 #4 |

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
|---|---|---|---|---|
| 7 | Behaviour when a sale would exceed a customer's credit limit | A tenant setting in given `cu` offering warn, or block with override by a permitted role. The default is block with override. Recorded as POL-PTY-008 | Converged in dialogue (round 2); user confirms | KB §6.2 ("nothing imposed"), §6.5 ("every error offers a next action"), §3 D8 (role presets) |
| 9 | Walk-in customer for POS sales without a named customer | Seeded per tenant by PTY, with no credit and protected from deletion. Recorded as POL-PTY-009 | Converged in dialogue (round 2); user confirms | KB §6.8, §6.10 |
══════════════════════════════════════════════════════════════════
