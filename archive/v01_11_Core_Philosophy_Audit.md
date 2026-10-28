# Atlas Core Philosophy — Architecture Audit

> نسخه: 1.0  
> تاریخ: 2026/08/27  
> وضعیت: Audit / Review Draft  
> سند مرجع: `10_Atlas_Core_Philosophy.md`  
> هدف: بررسی انسجام، ابهام‌ها، تناقض‌ها، فرضیات اثبات‌نشده و آمادگی سند Core Philosophy برای تبدیل شدن به Source of Truth

---

## 1. هدف Audit

این سند یک بازبینی معماری روی `10_Atlas_Core_Philosophy.md` است.

هدف آن بازنویسی سند اصلی نیست. هدف این است که مشخص شود آیا سند فعلی می‌تواند بدون ابهام، مبنای Blueprintهای بعدی Atlas قرار بگیرد یا خیر.

این Audit عمداً بین سه وضعیت تفاوت می‌گذارد:

- **Confirmed** — چیزی که در سند به‌صورت روشن و سازگار تعریف شده است.
- **Open / Unresolved** — موضوعی که سند عمداً یا ناخواسته باز گذاشته است.
- **Needs Validation** — فرضی که قبل از تبدیل شدن به تصمیم فنی باید با evidence، prototype، source code، documentation یا testing بررسی شود.

---

# 2. نتیجه اجرایی

`10_Atlas_Core_Philosophy.md` از نظر هویت محصول و جهت‌گیری مفهومی، سند سالم و قابل اتکایی است.

مهم‌ترین موفقیت آن این است که Atlas را از «Termux با UI زیباتر» جدا کرده و آن را به‌عنوان یک **Native Android Graphical Shell / UX Layer** تعریف می‌کند.

همچنین چند تصمیم کلیدی به‌درستی تثبیت شده‌اند:

- Atlas جایگزین Termux نیست.
- Atlas نباید بدون ضرورت Termux را fork کند.
- GUI مسیر اصلی عملیات رایج است.
- Terminal همچنان یک interface کامل و first-class است.
- Intent از syntax مهم‌تر است.
- Action از command implementation مهم‌تر است.
- Integration با Termux نباید بر اساس فرضیات اثبات‌نشده طراحی شود.
- Core نباید به انبار feature تبدیل شود.
- Pro باید بر پایه معماری Core ساخته شود.

با این حال، سند هنوز برای تبدیل شدن به «قرارداد معماری نهایی» کمی زود است.

مشکل اصلی سند فعلی تناقض جدی نیست؛ **ابهام در مرز چند مفهوم کلیدی** است:

1. تفاوت دقیق `Intent` و `Action`
2. تفاوت `Action Engine` و `Termux Adapter`
3. تعریف دقیق `Project` و `Workspace`
4. مفهوم `Context`
5. مرز بین Native Visual Action، Guided Action و Direct Terminal
6. نحوه حفظ context بین GUI و Terminal
7. معنای «قدرت کامل CLI» در کنار abstraction
8. نحوه تشخیص اینکه یک عملیات واقعاً قابل abstraction است
9. قرارداد خطا، progress و cancellation
10. جایگاه filesystem و permission model

بنابراین توصیه این Audit:

> **سند 10 را فعلاً به‌عنوان Core Philosophy تأیید می‌کنیم، اما آن را هنوز به‌عنوان قرارداد implementation نهایی تلقی نمی‌کنیم.**

---

# 3. وضعیت بخش‌های اصلی سند

| حوزه | وضعیت | ارزیابی |
|---|---|---|
| Product Identity | Confirmed | بسیار خوب و روشن |
| Product Boundary | Confirmed | خوب |
| GUI vs CLI philosophy | Confirmed | درست |
| Semantic Abstraction | Confirmed | یکی از مهم‌ترین تصمیم‌ها |
| Action concept | Confirmed / Needs expansion | ایده روشن، قرارداد هنوز ناقص |
| Project concept | Open | نیازمند مدل مستقل |
| Workspace concept | Open | برای آینده تعریف شده ولی هنوز دقیق نیست |
| Context | Open | اهمیتش روشن است، مدلش نه |
| Termux relationship | Confirmed | جهت‌گیری صحیح |
| Termux integration | Needs Validation | عمداً باز گذاشته شده |
| Terminal | Confirmed | first-class و نه fallback |
| Filesystem | Needs Validation | باید قرارداد دسترسی مشخص شود |
| Error philosophy | Confirmed | مناسب |
| Security philosophy | Confirmed | مناسب |
| Performance | Needs Validation | تصمیم درست برای عدم قفل کردن اعداد |
| Plugin | Deferred | تصمیم درست |
| AI | Deferred | تصمیم درست |
| Lite / Pro boundary | Partially Confirmed | هنوز نیازمند Capability Model |
| Feature criteria | Confirmed | مناسب |
| Success criteria | Confirmed | خوب |
| Architecture implementation | Deferred | تصمیم درست |

---

# 4. نقاط قوتی که نباید در بازبینی‌های بعدی خراب شوند

## 4.1 تعریف هویت

تعریف فعلی Atlas به‌اندازه کافی روشن است:

> Atlas یک Native Android Graphical Shell و UX Layer برای اکوسیستم Termux/Linux است.

این باید حفظ شود.

Atlas نباید در اسناد بعدی به یکی از این موارد تقلیل پیدا کند:

- GUI Termux
- Terminal emulator
- File Manager
- IDE
- Linux Desktop
- Command launcher

---

## 4.2 Command به‌عنوان implementation

این تصمیم باید به‌عنوان یک invariant معماری باقی بماند:

> **Command، implementation است؛ Action، abstraction است.**

این جمله احتمالاً یکی از مهم‌ترین اصول کل پروژه خواهد بود.

---

## 4.3 Terminal به‌عنوان مسیر کامل کنترل

سند به‌درستی Terminal را fallback صرف معرفی نمی‌کند.

این تصمیم مهم است، چون اگر GUI تنها مسیر باشد، Atlas در نهایت تبدیل به یک سیستم محدود و opinionated می‌شود.

مدل صحیح:

```text
GUI
  = Preferred visual interface

Terminal
  = Full-power interface
```

هر دو باید first-class باشند.

---

# 5. ابهام شماره 1 — Intent و Action هنوز دقیقاً جدا نشده‌اند

سند می‌گوید:

```text
Intent Layer
  ↓
Action Engine
```

اما تفاوت قراردادی این دو هنوز کاملاً مشخص نیست.

مثلاً:

```text
Clone Repository
```

آیا این یک Intent است؟

یا Action؟

در یک معماری پیشنهادی:

```text
User Intent
    ↓
"می‌خواهم این repository را clone کنم"
    ↓
CloneRepository Action
```

در این مدل:

**Intent** = هدف/درخواست کاربر

**Action** = واحد معنایی قابل اجرا در Atlas

این تفاوت باید در سند `12_Atlas_Action_Architecture.md` صریحاً تثبیت شود.

### وضعیت

**Open — Critical**

### دلیل اهمیت

اگر این دو مفهوم مبهم بمانند، UI، controller، Action Engine و AI آینده احتمالاً مرزهای متفاوتی برای آنها ایجاد خواهند کرد.

---

# 6. ابهام شماره 2 — Action Engine بیش از حد بزرگ تعریف شده است

در diagram فعلی Action Engine شامل این موارد است:

- Validation
- State
- Resolution
- Command Construction
- Result Mapping
- Error Mapping
- Progress
- Cancellation

این مجموعه ممکن است بیش از حد مسئولیت روی یک abstraction قرار دهد.

پرسش معماری:

آیا `Command Construction` واقعاً مسئولیت Action Engine است؟

یا:

```text
Action
 ↓
Execution Strategy
 ↓
Command Resolver
 ↓
Termux Adapter
```

بهتر است Action Engine orchestration را انجام دهد و implementation-specific command construction در لایه پایین‌تر قرار گیرد.

### وضعیت

**Open — High**

### تصمیم پیشنهادی

در `12_Atlas_Action_Architecture.md` مسئولیت هر لایه جدا شود.

---

# 7. ابهام شماره 3 — Context مدل نشده است

سند به‌درستی روی Context Preservation تأکید دارد، اما Context هنوز فقط مجموعه‌ای از مثال‌هاست:

- Project
- Working Directory
- Git repository
- Environment
- Terminal session
- Selected file

این کافی نیست.

باید مشخص شود:

```text
Atlas Context
├── Workspace
├── Project
├── Location
├── File
├── Repository
├── Environment
├── Session
└── Tool State
```

و سپس رابطه آنها مشخص شود.

مثلاً:

```text
Project
 ├── Root Path
 ├── Repository
 ├── Environment
 └── Sessions
```

### وضعیت

**Open — Critical**

این موضوع مستقیماً به Project/Workspace Model مربوط است.

---

# 8. ابهام شماره 4 — Project هنوز تعریف عملیاتی ندارد

سند می‌گوید Project صرفاً directory نیست.

این درست است.

اما هنوز معلوم نیست Project در Core دقیقاً چه چیزی است.

سه مدل ممکن:

### مدل A — Project = Directory + Metadata

ساده و مناسب Lite.

### مدل B — Project = Directory + Tooling Context

مثلاً:

- root
- environment
- git
- run configuration

### مدل C — Project = مستقل از filesystem

یعنی Project یک entity واقعی در Atlas باشد که می‌تواند چند مسیر و resource داشته باشد.

مدل C قدرتمندتر است ولی complexity زیادی دارد.

### وضعیت

**Open — Critical**

### توصیه

برای Core ابتدا مدل A/B بررسی شود و مدل C به آینده موکول شود مگر evidence خلاف آن را نشان دهد.

---

# 9. ابهام شماره 5 — Workspace زودتر از موعد وارد فلسفه شده است

تعریف Workspace مفید است، اما در Core Philosophy هنوز به‌اندازه Project ضروری نیست.

خطر این است که Blueprintهای بعدی برای یک قابلیت آینده، Core را پیچیده کنند.

### پیشنهاد

Workspace به‌عنوان مفهوم آینده حفظ شود، اما:

> هیچ بخش Core نباید برای وجود Workspace وابسته باشد.

### وضعیت

**Deferred**

---

# 10. ابهام شماره 6 — مرز abstraction نیاز به معیار عینی‌تر دارد

سه Tier فعلی بسیار مفیدند:

1. Native Visual Actions
2. Guided Actions
3. Direct Terminal

اما سؤال مهم باقی است:

> چه زمانی یک قابلیت از Tier 1 به Tier 2 یا Tier 3 می‌رود؟

در سند معیارهای عمومی وجود دارد، ولی یک decision framework لازم است.

مثلاً:

```text
Is operation frequent?
        ↓
Can semantics be modeled reliably?
        ↓
Can inputs be represented safely?
        ↓
Can result/error states be mapped?
        ↓
Can rollback/recovery be explained?
        ↓
Is UI meaningfully better than CLI?
```

اگر چند پاسخ منفی باشند، abstraction نباید اجباری شود.

### وضعیت

**Open — High**

---

# 11. ابهام شماره 7 — «قدرت کامل CLI» نیاز به تعریف دارد

سند می‌گوید کاربر حرفه‌ای باید به قدرت کامل CLI دسترسی داشته باشد.

این از نظر محصول درست است.

ولی باید مشخص شود منظور چیست:

- دسترسی به shell؟
- دسترسی به stdin/stdout؟
- اجرای هر executable نصب‌شده؟
- environment variables؟
- pipes؟
- redirection؟
- interactive programs؟
- terminal resize؟
- job control؟
- signals؟

این موارد در Terminal Specification مشخص خواهند شد، نه در Core Philosophy.

### وضعیت

**Deferred to Technical Architecture**

---

# 12. ابهام شماره 8 — Result Mapping هنوز مفهومی است

Action باید نتیجه واقعی Termux/Linux را به UI منتقل کند.

اما Result می‌تواند شامل موارد زیر باشد:

```text
Success
Partial Success
Warning
Failure
Cancelled
Timed Out
Unknown State
```

برای Atlas بهتر است Result model مستقل داشته باشیم.

### وضعیت

**Open — High**

### مقصد

`12_Atlas_Action_Architecture.md`

---

# 13. ابهام شماره 9 — Error Mapping نیازمند taxonomy است

اصل دو لایه‌ای بودن خطا بسیار خوب است:

```text
Human-readable
+
Technical details
```

اما باید بعداً error taxonomy ایجاد شود.

مثلاً:

```text
Permission
Tool Missing
Invalid Input
Network
Filesystem
Process
Termux Integration
User Cancelled
Unknown
```

این taxonomy باید بین UI و Action Engine مشترک باشد.

### وضعیت

**Open — Medium/High**

---

# 14. ابهام شماره 10 — Cancellation و Recovery

در Action Contract این موارد آمده‌اند:

- Cancellation
- Recovery

ولی مشخص نیست کدام عملیات قابل cancel هستند.

مثلاً:

```text
git clone
```

ممکن است قابل cancellation باشد.

اما:

```text
rm -rf ...
```

بعد از شروع الزاماً recovery ندارد.

پس Action باید capabilityهای خودش را اعلام کند.

مثلاً:

```text
supportsCancellation
supportsRecovery
supportsDryRun
isDestructive
```

این موضوع برای Security نیز مهم است.

### وضعیت

**Open — High**

---

# 15. ابهام شماره 11 — Security هنوز policy است، نه model

اصول امنیتی سند درست هستند، ولی هنوز Permission Model وجود ندارد.

در آینده باید مشخص شود:

```text
User
 ↓
Atlas Action
 ↓
Risk Classification
 ↓
Permission / Confirmation
 ↓
Execution
```

مثلاً:

### Low Risk

Read file

### Medium Risk

Install package

### High Risk

Delete directory

### Critical

Arbitrary shell command

این classification می‌تواند بعدها پایه confirmation UX و AI guardrails شود.

### وضعیت

**Needs dedicated design**

---

# 16. ابهام شماره 12 — Filesystem boundary

سند درست می‌گوید Atlas نباید filesystem را به abstraction مصنوعی تبدیل کند.

اما هنوز مشخص نیست Atlas دقیقاً چه سطحی از filesystem را مالک است.

باید بعداً روشن شود:

- چه pathهایی قابل مشاهده‌اند؟
- چه pathهایی قابل write هستند؟
- permissionها چگونه نمایش داده می‌شوند؟
- symlink چگونه نمایش داده می‌شود؟
- external storage چگونه مدیریت می‌شود؟
- Termux private directories چه وضعیتی دارند؟

این موضوع نباید در Core Philosophy حل شود؛ ولی باید در Integration Spec حل شود.

### وضعیت

**Needs Validation**

---

# 17. ابهام شماره 13 — Plugin و Action API

تصمیم فعلی برای defer کردن Plugin کاملاً درست است.

اما یک اصل باید حفظ شود:

> Plugin باید Action/Extension API را ببیند، نه internals را.

این تصمیم فعلاً مناسب است.

هیچ طراحی production برای Plugin قبل از تثبیت:

- Action
- Context
- Permission
- Lifecycle

نباید انجام شود.

### وضعیت

**Confirmed — Deferred**

---

# 18. ابهام شماره 14 — AI

جایگاه AI در سند به‌درستی محدود شده است.

مدل:

```text
User
 ↓
AI
 ↓
Intent
 ↓
Action Plan
 ↓
Atlas Actions
 ↓
Termux
```

این مدل باید حفظ شود.

AI نباید مسیر:

```text
AI → unrestricted shell
```

باشد.

### وضعیت

**Confirmed**

---

# 19. تناقض واقعی شماره 1 — «Native Android» در برابر Editor/Terminal پیچیده

در سند Atlas باید Native Android باشد و در عین حال Terminal و Editor و workflowهای پیچیده داشته باشد.

این تناقض واقعی نیست، ولی یک trade-off است.

اگر UI بیش از حد شبیه desktop شود:

> Atlas از Native Android experience فاصله می‌گیرد.

اگر بیش از حد mobile ساده شود:

> Power User experience آسیب می‌بیند.

این باید در UX Architecture با adaptive design حل شود.

### وضعیت

**Trade-off — Not a contradiction**

---

# 20. تناقض واقعی شماره 2 — «پنهان کردن implementation» در برابر transparency

سند می‌گوید commandها نباید قرارداد UX باشند، ولی security می‌گوید عملیات خطرناک نباید پنهان شوند.

این نیز تناقض نیست.

مدل درست:

```text
Default:
Semantic UI

Advanced:
Show what will happen

Expert:
Open Terminal / View command / View details
```

بنابراین abstraction باید:

**Hide syntax by default**

ولی:

**Never hide consequences**

را دنبال کند.

---

# 21. چیزی که باید به Core Philosophy اضافه شود

سند فعلی یک مفهوم بسیار مهم را کم دارد:

## User Control & Reversibility

Atlas نباید فقط ساده باشد.

باید قابل فهم و قابل کنترل هم باشد.

برای عملیات مهم:

```text
Preview
Confirm
Execute
Observe
Undo / Recovery when possible
```

این اصل مخصوصاً برای File، Git، Package و AI آینده اهمیت دارد.

---

# 22. اصل جدید پیشنهادی — Capability over Feature

در Blueprintهای آینده بهتر است به جای فکر کردن صرفاً به featureها، capabilityهای بنیادی را تعریف کنیم.

مثلاً:

```text
Filesystem Capability
Process Capability
Package Capability
Git Capability
Network Capability
Terminal Capability
Project Capability
```

سپس featureها از capabilityها ساخته شوند.

این باعث می‌شود:

```text
Clone Repository
```

یک feature باشد که از:

```text
Network
Filesystem
Process
Git
Context
```

استفاده می‌کند.

این نگاه برای معماری بسیار مقیاس‌پذیرتر است.

---

# 23. اصل جدید پیشنهادی — Native UI is not Native Logic

Atlas باید Native Android باشد، اما business logic نباید به UI componentها وابسته شود.

مدل مطلوب:

```text
Compose / Android UI
        ↓
Intent
        ↓
Action
        ↓
Core
        ↓
Adapter
        ↓
Termux
```

نه:

```text
Button
 ↓
execute shell command
```

این اصل باید در Architecture Blueprint ثبت شود.

---

# 24. اصل جدید پیشنهادی — Same Action, Multiple Interfaces

اگر ممکن باشد، یک Action باید بتواند از چند interface فراخوانی شود:

```text
GUI
 ↓
Action

Terminal / Command Palette
 ↓
Action

Future AI
 ↓
Action

Future Automation
 ↓
Action
```

این یکی از مهم‌ترین دلایل ارزشمند بودن Action Layer است.

---

# 25. اصل جدید پیشنهادی — Observable Execution

Atlas نباید عملیات را به black box تبدیل کند.

هر Action در حد مناسب باید وضعیت خود را قابل مشاهده کند:

```text
Queued
Running
Progress
Completed
Failed
Cancelled
```

برای عملیات طولانی:

```text
Progress
Current Step
Output
Error
Cancel
```

این اصل باید به Action Contract اضافه شود.

---

# 26. تصمیم‌هایی که همین حالا باید قفل شوند

این موارد به‌اندازه کافی روشن هستند و می‌توانند invariant باشند:

1. Atlas جایگزین Termux نیست.
2. Atlas نباید بدون ضرورت Termux را fork کند.
3. Termux/Linux execution backend است.
4. GUI مسیر اصلی عملیات رایج است.
5. Terminal first-class است.
6. Intent بالاتر از syntax است.
7. Action abstraction اصلی عملیات است.
8. Command implementation است.
9. abstraction نباید semantics ابزار زیرین را تغییر دهد.
10. هر abstraction باید escape hatch داشته باشد.
11. API assumptions باید validation شوند.
12. Core نباید feature dump شود.
13. AI مستقیماً shell نامحدود را کنترل نمی‌کند.
14. Plugin قبل از تثبیت Core contracts ساخته نمی‌شود.
15. امنیت و transparency نباید فدای simplicity شوند.

---

# 27. تصمیم‌هایی که هنوز نباید قفل شوند

این موارد عمداً باز بمانند:

- PTY implementation
- Transport mechanism
- Session API
- File access mechanism
- minSdk
- Database
- Performance budgets
- Plugin runtime
- AI provider
- Exact package structure
- Exact Compose architecture
- Workspace implementation
- Advanced project metadata

---

# 28. مواردی که باید در Blueprintهای بعدی حل شوند

## `11_Atlas_Capability_Model.md`

باید حل کند:

- Core capabilities
- Capability boundaries
- Visual / Guided / Terminal tiers
- Lite / Pro mapping
- Capability dependencies
- Feature admission criteria

---

## `12_Atlas_Action_Architecture.md`

باید حل کند:

- Intent definition
- Action definition
- Action lifecycle
- Action contract
- Input/output
- Validation
- Result
- Error
- Progress
- Cancellation
- Recovery
- Destructive actions
- Action composition
- Action context

---

## `13_Termux_Integration_Spec.md`

باید حل کند:

- Command execution
- Interactive sessions
- stdin/stdout/stderr
- PTY
- session lifecycle
- process signals
- filesystem access
- permissions
- external app integration
- official vs unsupported mechanisms

---

## `14_Project_Workspace_Model.md`

باید حل کند:

- Project identity
- Root path
- Metadata
- Environment
- Git context
- Session association
- Workspace
- Recent state
- persistence

---

# 29. ترتیب اصلاح پیشنهادی

ترتیب نباید عوض شود:

```text
10 Core Philosophy
        ↓
11 Capability Model
        ↓
12 Action Architecture
        ↓
13 Termux Integration
        ↓
14 Project / Workspace
        ↓
Lite Blueprint
        ↓
UI / Component
        ↓
Roadmap
        ↓
Pro
```

دلیل:

اگر قبل از Capability Model سراغ Lite برویم، احتمالاً دوباره featureها را زودتر از معماری تعریف خواهیم کرد.

اگر قبل از Action Architecture سراغ API برویم، integration را بر اساس commandها طراحی می‌کنیم.

اگر قبل از Integration Spike سراغ implementation برویم، احتمال دارد روی فرضیات Termux سرمایه‌گذاری کنیم.

---

# 30. آیا `10_Atlas_Core_Philosophy.md` باید همین الان اصلاح شود؟

**توصیه: نه، فعلاً نه.**

نسخه فعلی برای هدف خود یعنی «تعریف هویت و اصول بنیادی» موفق است.

بهتر است آن را با جزئیات معماری سنگین نکنیم.

مواردی که در این Audit شناسایی شدند باید در Blueprintهای تخصصی بعدی حل شوند.

تنها در صورتی باید سند 10 اصلاح شود که یک contradiction واقعی یا تغییر در Product Identity کشف شود.

در Audit فعلی چنین contradiction بنیادی پیدا نشد.

---

# 31. Verdict نهایی

## وضعیت سند

**APPROVED AS CONCEPTUAL SOURCE OF TRUTH**

اما:

**NOT YET AN IMPLEMENTATION CONTRACT**

یعنی:

```text
هویت Atlas
       ↓
        ✓ تثبیت شده

فلسفه UX
       ↓
        ✓ تثبیت شده

مرز Atlas / Termux
       ↓
        ✓ تثبیت شده

Semantic Action Model
       ↓
        ✓ جهت درست، نیازمند specification

Termux Integration
       ↓
        ⚠ نیازمند validation

Project / Workspace
       ↓
        ⚠ نیازمند مدل مستقل

Security / Permission
       ↓
        ⚠ نیازمند specification
```

---

# 32. مهم‌ترین نتیجه Audit

بعد از این بررسی، من **هیچ دلیل معماری برای کنار گذاشتن مدل اصلی Atlas نمی‌بینم.**

برعکس، مدل فعلی یک ستون فقرات بسیار خوب دارد:

```text
User
 ↓
Atlas
 ↓
Intent
 ↓
Action
 ↓
Termux Adapter
 ↓
Termux / Linux
```

اما برای اینکه این مدل در عمل هم درست کار کند، چهار ستون بعدی باید دقیق ساخته شوند:

```text
Capability
Action
Integration
Context
```

اگر این چهار مورد درست طراحی شوند، Blueprintهای Lite و Pro بعدی دیگر بر اساس «لیست قابلیت‌ها» ساخته نمی‌شوند؛ بر اساس یک معماری واحد ساخته خواهند شد.

---

# 33. Gate قبل از Blueprint بعدی

قبل از شروع `11_Atlas_Capability_Model.md` فقط این سؤال معماری باید پاسخ داده شده باشد:

> **آیا با تعریف فعلی موافقیم که Atlas یک Action-oriented Graphical Shell است، نه یک command-oriented GUI؟**

این Audit بر اساس پاسخ «بله» نوشته شده است.

اگر این اصل تأیید شود، مسیر بعدی مشخص است:

**Capability Model → Action Architecture → Termux Integration → Project/Workspace → Blueprint Revision**

---

*پایان سند — Atlas Core Philosophy Architecture Audit v1.0*
