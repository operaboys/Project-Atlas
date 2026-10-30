# Atlas — Capability Model

> سند: `02_Capability_Model.md`
> Version: `1.0-draft`
> Date: 2026-08-27
> Status: **Architecture Draft / Decision Candidate**
> Position in architecture sequence: **02 — Capability Model**
> Depends on: `01_Core_Philosophy.md`

---

# 1. Purpose

این سند مدل Capability در Atlas را تعریف می‌کند.

هدف Capability Model این نیست که فهرست ویژگی‌های Atlas را دوباره بنویسد.

هدف آن تعریف توانمندی‌های بنیادی و قابل ترکیب سیستم است تا Featureهای آینده از روی آنها ساخته شوند.

اصل مرکزی:

> **Capability over Feature**

به این معنا که Atlas نباید معماری خود را حول فهرست Featureها بنا کند.

به‌جای:

```text
Git Center
SSH Manager
Package Center
File Manager
Project Manager
...
```

باید ابتدا توانمندی‌های بنیادی مشخص شوند:

```text
Filesystem
Process
Network
Package
Git
Terminal
Project
Context
```

سپس هر Feature از یک یا چند Capability استفاده کند.

مثال:

```text
Clone Repository
```

می‌تواند از:

```text
Git
Network
Filesystem
Process
Context
```

استفاده کند.

---

# 2. Architectural Context

مدل معماری فعلی Atlas:

```text
Atlas UI
    ↓
User Intent
    ↓
Action Layer / Action Engine
    ↓
Termux Adapter
    ↓
Termux / Linux
    ↓
CLI Tools
```

Capability Model باید بین Product/UX و Action Architecture نقش مشخصی داشته باشد.

مدل مفهومی:

```text
User
  ↓
Intent
  ↓
Action
  ↓
Required Capabilities
  ↓
Execution / Adapter
  ↓
Termux / Linux
```

بنابراین:

- Intent بیان‌کننده خواسته یا هدف کاربر است.
- Action بیان‌کننده عملیات معنایی قابل اجرا است.
- Capability بیان‌کننده توانمندی موردنیاز برای انجام آن عملیات است.
- Command فقط یکی از implementationهای ممکن برای اجرای Action است.

اصل ثابت:

> **Command is implementation. Action is abstraction. Capability is system ability.**

---

# 3. Scope

این سند مشخص می‌کند:

- Capability چیست.
- چه چیزی Capability محسوب می‌شود.
- مرز Capabilityها چگونه تعیین می‌شود.
- Capabilityها چگونه به Actionها متصل می‌شوند.
- Capability چگونه به Tierهای تعامل مربوط می‌شود.
- Capability چگونه برای پذیرش Feature جدید استفاده می‌شود.
- چه Capabilityهایی در هسته مفهومی Atlas قرار می‌گیرند.
- چه مواردی فعلاً فقط Candidate هستند.
- چه چیزهایی عمداً خارج از Scope این سند باقی می‌مانند.

این سند هنوز مشخص نمی‌کند:

- API نهایی Actionها.
- implementation نهایی Termux integration.
- PTY implementation.
- Session protocol نهایی.
- Project/Workspace model نهایی.
- Permission API نهایی.
- Plugin API.
- AI architecture.
- package structure دقیق Android.

این موارد در اسناد تخصصی بعدی بررسی می‌شوند.

---

# 4. Definition of Capability

در Atlas:

> **Capability یک توانمندی بنیادی و قابل استفاده مجدد است که یک یا چند Action برای تحقق هدف خود به آن نیاز دارند.**

Capability نباید صرفاً نام یک Feature باشد.

مثال:

```text
Git
```

Capability است.

اما:

```text
Clone Repository
```

Feature/Action-level behavior است که از چند Capability استفاده می‌کند.

به همین ترتیب:

```text
Filesystem
```

Capability است.

اما:

```text
Rename File
```

یک Action است که از Filesystem Capability استفاده می‌کند.

---

# 5. Capability vs Action vs Command vs Feature

این چهار مفهوم نباید با یکدیگر مخلوط شوند.

## 5.1 Intent

Intent بیان می‌کند کاربر چه می‌خواهد.

مثال:

```text
"می‌خواهم یک repository را دریافت کنم."
```

Intent هنوز الزاماً مشخص نمی‌کند عملیات دقیق چگونه اجرا شود.

---

## 5.2 Action

Action یک عملیات معنایی و قابل اجراست.

مثال:

```text
CloneRepository
```

Action باید contract مشخص داشته باشد.

جزئیات کامل contract در:

`03_Action_Architecture.md`

تعریف خواهد شد.

---

## 5.3 Capability

Capability توانمندی موردنیاز سیستم برای اجرای Action است.

مثلاً:

```text
CloneRepository
```

به‌صورت مفهومی ممکن است نیازمند:

```text
Git
Network
Filesystem
Process
Context
```

باشد.

---

## 5.4 Command

Command implementation است.

مثلاً یک implementation ممکن است در نهایت به commandهایی مانند:

```text
git clone ...
```

منتهی شود.

اما:

```text
git clone ...
```

نباید قرارداد UI یا abstraction اصلی Atlas باشد.

---

## 5.5 Feature

Feature یک تجربه یا قابلیت محصولی قابل مشاهده برای کاربر است.

مثلاً:

```text
Git Center
```

می‌تواند مجموعه‌ای از Actionها را ارائه کند:

```text
Clone Repository
Pull
Push
Create Branch
Switch Branch
View Status
```

Feature از Capabilityها استفاده می‌کند؛ Capability نباید از Feature list استخراج شود.

---

# 6. Capability Design Principles

Capabilityهای Atlas باید این اصول را رعایت کنند.

## 6.1 Reusability

یک Capability باید توسط چند Action قابل استفاده باشد.

مثال:

```text
Filesystem
```

می‌تواند در:

```text
Create File
Rename File
Delete File
Clone Repository
Create Project
Import Project
```

استفاده شود.

---

## 6.2 Semantic Stability

Capability باید تا حد ممکن معنای مستقل از implementation داشته باشد.

مثلاً:

```text
Filesystem
```

نباید در تعریف خود به یک command خاص مانند `cp` یا `mv` وابسته شود.

---

## 6.3 Implementation Independence

Capability نباید implementation را به UX تحمیل کند.

ممکن است:

```text
Git Capability
```

امروز از CLI استفاده کند و در آینده از implementation دیگری.

Action contract نباید صرفاً به دلیل تغییر implementation شکسته شود.

---

## 6.4 Composability

Capabilityها باید قابل ترکیب باشند.

مثال:

```text
CloneRepository
    ├── Git
    ├── Network
    ├── Filesystem
    ├── Process
    └── Context
```

---

## 6.5 Observable Behavior

Capability باید در حد لازم بتواند وضعیت اجرای Action را برای Action Layer قابل مشاهده کند.

برای مثال:

```text
Queued
Running
Progress
Completed
Failed
Cancelled
```

مدل کامل Result و Lifecycle در Action Architecture مشخص خواهد شد.

---

## 6.6 Safe Abstraction

Capability نباید به‌خاطر ساده‌سازی، consequence عملیات را پنهان کند.

اصل:

> **Hide syntax by default. Never hide consequences.**

برای عملیات حساس، abstraction باید امکان:

```text
Preview
Confirm
Execute
Observe
Recovery / Undo when possible
```

را فراهم کند.

---

## 6.7 Escape Hatch

هر abstraction مهم باید راهی برای خروج به سطح پایین‌تر داشته باشد.

مدل تعامل:

```text
Native Visual Action
        ↓
Guided Action
        ↓
Direct Terminal
```

کاربر نباید در abstraction Atlas زندانی شود.

---

# 7. Capability Tiers

Tierهای تعامل Atlas نباید با Capabilityها اشتباه گرفته شوند.

Capability پاسخ می‌دهد:

> سیستم چه توانمندی‌ای دارد؟

Interaction Tier پاسخ می‌دهد:

> این توانمندی یا Action چگونه به کاربر ارائه شود؟

سه Tier فعلی:

## Tier 1 — Native Visual Actions

برای عملیات پرتکرار، قابل مدل‌سازی و نسبتاً قابل پیش‌بینی.

مثال:

```text
Rename File
Create Project
Clone Repository
Connect Server
```

---

## Tier 2 — Guided Actions

برای عملیات پیچیده‌تر یا دارای variability بیشتر.

Atlas فرم، wizard، validation یا workflow ارائه می‌دهد.

---

## Tier 3 — Direct Terminal

برای مواردی که:

- abstraction ارزش کافی ندارد.
- semantics پیچیده یا نامطمئن است.
- variability زیاد است.
- کاربر کنترل کامل می‌خواهد.
- مدل‌سازی امن و قابل اعتماد دشوار است.

Terminal در این مدل fallback ضعیف نیست.

> **Terminal is a first-class interface.**

---

# 8. Core Capability Set

Capabilityهای زیر به‌عنوان **Core Candidates** شناخته می‌شوند.

این فهرست بر اساس Core Philosophy، Audit و Blueprintهای موجود شکل گرفته است و هنوز به معنی implementation نهایی نیست.

```text
1. Filesystem
2. Process
3. Network
4. Package
5. Git
6. Terminal
7. Project
8. Context
```

اینها باید پایه بررسی معماری بعدی باشند.

---

# 9. Filesystem Capability

## Purpose

توانایی مشاهده و انجام عملیات مجاز روی منابع فایل و directory.

## Candidate responsibilities

```text
Read
Write
Create
Delete
Rename
Move
Copy
List
Inspect
Preview
Path resolution
```

## Important boundary

Filesystem Capability نباید فرض کند Atlas همیشه دسترسی مستقیم به Termux private filesystem دارد.

مستندات معماری صراحتاً نشان می‌دهند که دسترسی فایل میان Android و Termux به دلیل isolation و Scoped Storage نیازمند طراحی و validation است.

بنابراین:

```text
Filesystem Capability
        ↓
Filesystem Adapter
        ↓
Validated access mechanism
```

مکانیزم دقیق در:

`04_Termux_Integration.md`

تعیین خواهد شد.

## Security sensitivity

تقریب اولیه:

```text
Read        → Low
Write       → Medium
Rename      → Medium
Move        → Medium
Copy        → Medium
Delete      → High
Recursive Delete → High/Critical
```

این classification فعلاً Proposal است، نه Security Contract.

---

# 10. Process Capability

## Purpose

توانایی ایجاد، کنترل و مشاهده processهای Linux/Termux.

## Candidate responsibilities

```text
Start
Observe
Receive output
Receive errors
Cancel
Terminate
Track lifecycle
```

Process Capability با Terminal Capability یکی نیست.

Process می‌تواند برای یک اجرای one-shot یا background استفاده شود، در حالی که Terminal یک محیط interactive و first-class است.

## Example

```text
Run Python Script
```

می‌تواند از:

```text
Process
Filesystem
Context
```

استفاده کند.

---

# 11. Network Capability

## Purpose

ارائه توانمندی‌های شبکه‌ای موردنیاز Actionها.

## Candidate responsibilities

```text
Connectivity
Network request
Remote resource access
Connection state
Timeout handling
```

این Capability نباید semantics ابزارهای زیرین را بی‌دلیل بازتعریف کند.

## Example

```text
Clone Repository
```

می‌تواند Network را برای دسترسی به remote repository مصرف کند.

---

# 12. Package Capability

## Purpose

ارائه abstraction برای عملیات مربوط به packageها، بدون جایگزین کردن package managerهای Linux/Termux.

## Candidate responsibilities

```text
Inspect package
Install package
Remove package
Update package
Search package
List installed packages
```

## Important boundary

Atlas نباید package managerهای زیرین را replace کند.

Capability فقط عملیات را model می‌کند.

Implementation می‌تواند به package manager موجود در Termux/Linux واگذار شود.

## Tier expectation

بسته به reliability و semantics:

```text
Common package operations
    → Native / Guided

Advanced package workflows
    → Guided / Terminal

Unsupported or ambiguous semantics
    → Direct Terminal
```

---

# 13. Git Capability

## Purpose

ارائه توانمندی‌های Git به‌عنوان یک سیستم version control، بدون تبدیل Git به مجموعه‌ای از command stringها در UI.

## Candidate responsibilities

```text
Repository inspection
Clone
Init
Status
Branch
Checkout / Switch
Commit
Pull
Push
Fetch
Merge
Diff
Log
Remote management
```

این فهرست Candidate است و به معنی scope نهایی Git Center نیست.

## Example

```text
Clone Repository
```

ترکیب:

```text
Git
Network
Filesystem
Process
Context
```

خواهد بود.

---

# 14. Terminal Capability

## Purpose

ارائه دسترسی کامل و first-class به interactive Linux/Termux environment.

## Candidate responsibilities

```text
Interactive shell
stdin
stdout
stderr
PTY
Session
Signals
Environment
Working directory
Pipes
Redirection
Interactive programs
```

## Important distinction

Terminal Capability نباید به‌عنوان «راه فرار از شکست GUI» تعریف شود.

Terminal از ابتدا یکی از دو مسیر اصلی Atlas است:

```text
GUI / Action
+
Direct Terminal
```

## Technical status

جزئیات PTY، socket، session lifecycle و transport هنوز validated نیستند.

بنابراین موارد زیر در این سند:

```text
PTY
Socket
Session API
```

صرفاً capability requirements هستند، نه تصمیم implementation نهایی.

---

# 15. Project Capability

## Purpose

ارائه توانمندی‌های مربوط به مفهوم Project در Atlas.

## Candidate responsibilities

```text
Create Project
Open Project
Inspect Project
Associate resources
Remember project state
Launch project-related actions
```

## Important unresolved issue

Project هنوز تعریف نهایی ندارد.

گزینه‌های بررسی‌شده:

```text
A: Directory + Metadata

B: Directory + Tooling Context

C: Independent entity with multiple resources
```

در وضعیت فعلی:

> **Model A یا B باید قبل از C بررسی شود.**

Project Capability بنابراین فعلاً باید به اندازه‌ای abstraction داشته باشد که به مدل نهایی Project وابسته نشود.

جزئیات در:

`05_Project_Workspace.md`

تعیین خواهد شد.

---

# 16. Context Capability

Context در Atlas صرفاً یک feature نیست.

Context یک مفهوم cross-cutting است که می‌تواند اطلاعات موردنیاز Actionها را فراهم کند.

Context candidate:

```text
Workspace
Project
Location
File
Repository
Environment
Session
Tool State
```

## Current status

مدل Context هنوز نهایی نشده است.

بنابراین:

> Context فعلاً یک **Core Architectural Candidate** است، نه یک API نهایی.

## Why it matters

مثلاً:

```text
CloneRepository
```

بدون context ممکن است نداند:

```text
Destination
Current Project
Current Directory
Repository
Environment
```

کدام مورد relevant است.

Context باید dependency را به‌شکل semantic ارائه کند و نباید UI state را مستقیماً به backend نشت دهد.

---

# 17. Capability Dependency Model

Capabilityها باید قابلیت composition داشته باشند.

نمونه مفهومی:

```text
CreatePythonProject
    ├── Project
    ├── Filesystem
    ├── Process
    └── Context
```

```text
CloneRepository
    ├── Git
    ├── Network
    ├── Filesystem
    ├── Process
    └── Context
```

```text
InstallPackage
    ├── Package
    ├── Process
    └── Context
```

```text
RenameFile
    ├── Filesystem
    └── Context
```

```text
OpenInteractiveShell
    ├── Terminal
    ├── Process
    └── Context
```

این dependency graph باید در Action Architecture رسمی‌تر شود.

---

# 18. Capability Ownership

Capability نباید مسئول همه چیز باشد.

مدل پیشنهادی:

```text
Capability
    ↓
Defines what is possible

Action
    ↓
Defines what operation is being requested

Adapter
    ↓
Defines how it reaches execution backend

UI
    ↓
Defines how the operation is presented
```

مثلاً:

```text
Filesystem
```

نباید:

- UI بسازد.
- navigation را کنترل کند.
- command string را به UI تحمیل کند.
- Android Compose state را مدیریت کند.

---

# 19. Capability Contract — Candidate

هر Capability در آینده باید بتواند حداقل این اطلاعات مفهومی را بیان کند:

```text
Capability Identity
Purpose
Supported Operations
Required Context
Dependencies
Constraints
Supported Interaction Tiers
Observability
Cancellation Support
Recovery Support
Risk Characteristics
Availability
```

این ساختار فعلاً **Candidate Contract** است.

نباید قبل از Action Architecture به API نهایی تبدیل شود.

---

# 20. Capability Availability

Capability ممکن است در یک محیط موجود باشد و در محیط دیگر در دسترس نباشد.

مثال:

```text
Git Capability
```

ممکن است به نصب بودن Git در محیط Termux وابسته باشد.

بنابراین بهتر است در آینده distinction زیر وجود داشته باشد:

```text
Capability exists
        ≠
Capability currently available
```

مثلاً:

```text
Git
    Present
    Missing
    Incompatible
    Unknown
```

مدل کامل availability در Action Architecture / Termux Integration مشخص خواهد شد.

---

# 21. Capability Discovery

Atlas نباید صرفاً از روی نصب بودن یک feature نتیجه بگیرد که capability قابل استفاده است.

Capability discovery باید تا حد ممکن بر evidence متکی باشد.

مثلاً:

```text
Is Git available?
    ↓
Validate
    ↓
Version / executable / environment
    ↓
Capability state
```

این اصل با تصمیم کلی پروژه درباره:

> **API assumptions must be validated**

هم‌راستا است.

---

# 22. Feature Admission Model

هر Feature جدید نباید صرفاً به این دلیل وارد Atlas شود که «جالب است».

قبل از admission باید مشخص شود:

```text
Feature
   ↓
Which Action(s)?
   ↓
Which Capability(s)?
   ↓
Are semantics reliable?
   ↓
Can inputs be modeled safely?
   ↓
Can outputs/errors be mapped?
   ↓
Can cancellation/recovery be handled?
   ↓
Is GUI meaningfully better?
   ↓
Choose interaction tier
```

اگر پاسخ مناسب نباشد:

```text
Native Visual
```

نباید اجباری باشد.

Feature می‌تواند:

```text
Guided
```

یا:

```text
Direct Terminal
```

باقی بماند.

---

# 23. Capability Admission Checklist

برای اضافه کردن Capability جدید:

- [ ] Capability یک توانمندی بنیادی است، نه صرفاً نام یک Feature.
- [ ] حداقل یک Action واقعی از آن استفاده می‌کند.
- [ ] احتمال استفاده مجدد وجود دارد.
- [ ] semantics آن قابل تعریف است.
- [ ] implementation خاصی به تعریف آن تحمیل نشده است.
- [ ] dependencies آن مشخص شده‌اند.
- [ ] failure modes آن قابل بررسی هستند.
- [ ] availability آن قابل تشخیص یا حداقل قابل گزارش است.
- [ ] interaction tier مناسب آن مشخص شده است.
- [ ] security/risk آن قابل ارزیابی است.
- [ ] در صورت abstraction، escape hatch مناسب وجود دارد.
- [ ] ورود آن باعث scope creep غیرضروری در Core نمی‌شود.

---

# 24. Capability vs Feature Scope

Blueprintهای قبلی تعداد زیادی Feature برای Lite و مخصوصاً Pro تعریف کرده‌اند.

این سند آن فهرست را به‌عنوان معماری نهایی تأیید نمی‌کند.

به‌خصوص موارد زیر هنوز نباید به‌عنوان Core Capability تلقی شوند:

```text
AI
Plugin Marketplace
Cloud
Collaboration
Enterprise
Docker/Podman
CI/CD
Database Management
Scheduler
Monitoring
Remote Development
```

اینها ممکن است در آینده روی Capabilityهای Core یا Capabilityهای جدید بنا شوند، اما فعلاً admission نشده‌اند.

---

# 25. Lite / Pro Mapping

Capability Model باید قبل از Lite/Pro تصمیم‌گیری شود.

اصل پیشنهادی:

```text
Lite / Pro
```

نباید دو معماری متفاوت باشند.

آنها باید دو سطح product scope روی یک architecture مشترک باشند.

مثال مفهومی:

```text
Core Architecture
        ↓
Capabilities
        ↓
Actions
        ↓
Lite / Pro interfaces and scope
```

بنابراین:

```text
Git = architecture capability
```

و اینکه:

```text
Git Center
```

در کدام نسخه محصول ارائه شود، تصمیم product scope است؛ نه تعریف Capability.

---

# 26. Security Boundary

Capability Model به‌تنهایی Permission Model نیست.

اما هر Capability باید امکان ارزیابی risk را فراهم کند.

مدل مفهومی:

```text
User
 ↓
Intent
 ↓
Action
 ↓
Required Capabilities
 ↓
Risk Classification
 ↓
Permission / Confirmation
 ↓
Execution
```

مثلاً به‌صورت Proposal:

```text
Read File
    → Low

Install Package
    → Medium

Delete Directory
    → High

Arbitrary Shell
    → Critical
```

این classification نهایی نیست و در Security/Action Architecture باید validate شود.

---

# 27. Destructive Capabilities

Capabilityهایی که می‌توانند اثر مخرب یا برگشت‌ناپذیر ایجاد کنند باید metadata یا policy مناسب داشته باشند.

نمونه:

```text
Filesystem.Delete
Git.Reset
Git.Clean
Package.Remove
Process.Terminate
```

در این موارد:

```text
Preview
Confirmation
Execution visibility
Recovery when possible
```

باید در طراحی Action مربوطه بررسی شود.

اصل:

> **User Control & Reversibility**

---

# 28. Capability and Transparency

Atlas نباید implementation را به‌صورت پیش‌فرض روی صورت کاربر بکوبد.

اما abstraction نباید consequence را مخفی کند.

مدل UX:

```text
Default
Semantic explanation

Advanced
Show operation details

Expert
View command / open terminal
```

این اصل به‌خصوص برای:

```text
Filesystem
Git
Package
Network
Future AI
```

اهمیت دارد.

---

# 29. Observable Capability

Capabilityهایی که عملیات طولانی یا stateful ایجاد می‌کنند باید امکان مشاهده وضعیت را داشته باشند.

حداقل مدل مفهومی:

```text
Queued
Running
Completed
Partial Success
Warning
Failed
Cancelled
Timed Out
Unknown
```

برای عملیات طولانی:

```text
Progress
Current Step
Output
Error
Cancel
```

این مدل هنوز implementation contract نیست و در Action Architecture نهایی می‌شود.

---

# 30. Capability Failure Categories

Failureها باید تا حد امکان قابل دسته‌بندی باشند.

Candidate taxonomy:

```text
Permission
Tool Missing
Invalid Input
Network
Filesystem
Process
Termux Integration
User Cancelled
Timeout
Unknown
```

Capability باید failure را به شکلی ارائه کند که Action Layer بتواند آن را به Result/Error مناسب تبدیل کند.

---

# 31. Capability and Termux Boundary

Capability Model نباید Termux را از نظر semantic به Atlas تبدیل کند.

مدل درست:

```text
Atlas Capability
       ↓
Atlas Action
       ↓
Termux Adapter
       ↓
Termux / Linux
```

مدل نادرست:

```text
Atlas Feature
       ↓
Hard-coded Termux command
```

این distinction برای جلوگیری از architecture drift حیاتی است.

---

# 32. Legacy Blueprint Reconciliation

سند بایگانی‌شده `archive/v01_08_Architecture.md` از مفاهیمی مانند:

```text
Command Bridge
Quick Exec
PTY / Socket Stream
Session API
```

استفاده می‌کند.

این مفاهیم برای فهم وضعیت تاریخی پروژه مهم‌اند، اما در مدل جدید نباید مستقیماً به‌عنوان Capability contract تلقی شوند.

مثلاً:

```text
Quick Exec
```

implementation/transport concern است.

Capability بالاتر:

```text
Process
```

است.

و Action بالاتر:

```text
RunScript
```

است.

به‌صورت مفهومی:

```text
RunScript
   ↓
Process Capability
   ↓
Termux Adapter
   ↓
Quick Exec / another validated mechanism
```

این mapping باید در `03_Action_Architecture.md` دقیق شود.

---

# 33. Legacy File Access Reconciliation

سند بایگانی‌شده `archive/v01_09_API_Integration.md` برای File Access راهکارهایی مانند:

```text
Termux:API FileProvider
Daemon / Local Bridge
Shared UID / Signature
```

را مطرح کرده است.

این سند هیچ‌کدام را به‌عنوان حقیقت نهایی Capability Model قفل نمی‌کند.

Capability:

```text
Filesystem
```

باید مستقل از mechanism باقی بماند.

مکانیزم واقعی باید در Integration Specification با documentation، source code و prototype validation شود.

---

# 34. Important Architectural Correction

یکی از مهم‌ترین اصلاحات نسبت به Blueprintهای قدیمی:

قبلاً ساختار به‌شکل زیر بیان شده بود:

```text
UI
 ↓
Controller
 ↓
Command Bridge
 ↓
Termux
```

مدل فعلی باید به سمت این abstraction حرکت کند:

```text
UI
 ↓
Intent
 ↓
Action
 ↓
Capabilities
 ↓
Execution Adapter
 ↓
Termux
```

این به معنی حذف فوری تمام اجزای قبلی نیست.

به معنی تغییر **architectural ownership** است.

برای مثال:

```text
Command Bridge
```

می‌تواند در implementation به‌عنوان adapter/transport باقی بماند، ولی نباید abstraction اصلی UX باشد.

---

# 35. Capability Registry — Future Candidate

در آینده ممکن است Atlas به یک registry منطقی برای Capabilityها نیاز داشته باشد.

مثلاً:

```text
Filesystem
Process
Network
Git
Package
Terminal
Project
Context
```

اما این سند توصیه نمی‌کند که همین حالا یک global registry implementation ساخته شود.

قبل از آن باید:

```text
Capability semantics
Action contract
Availability model
Lifecycle
Permission model
```

تثبیت شوند.

---

# 36. Capability Composition

Actionها ممکن است چند Capability را ترکیب کنند.

مثال:

```text
CreatePythonProject

Project
  +
Filesystem
  +
Process
  +
Context
```

این composition نباید باعث coupling شدید Capabilityها شود.

بهتر است:

```text
Action
 ├── requests Capability A
 ├── requests Capability B
 └── coordinates result
```

نه:

```text
Filesystem → directly calls Git → directly calls Process
```

Action/Execution orchestration باید مالک composition باشد.

---

# 37. Capability Does Not Mean UI

هر Capability الزاماً صفحه مستقل ندارد.

مثلاً:

```text
Process
```

ممکن است هیچ صفحه‌ای نداشته باشد.

ممکن است صرفاً backend capability باشد که چند Action از آن استفاده کنند.

به همین شکل:

```text
Context
```

ممکن است کاملاً cross-cutting باشد.

این distinction جلوی تبدیل Capability Model به menu architecture را می‌گیرد.

---

# 38. Capability Does Not Mean Plugin

Capability باید بخشی از معماری Core باشد، اما این به معنی باز کردن Core برای Pluginها نیست.

Plugin system فعلاً Deferred است.

اصل فعلی:

> Plugin باید در آینده به Action/Extension API دسترسی داشته باشد، نه internals Atlas.

بنابراین:

```text
Capability Model
```

نباید اکنون به:

```text
Plugin SDK
```

وابسته شود.

---

# 39. AI Compatibility

AI فعلاً Deferred است.

اما Capability Model باید طوری طراحی شود که در آینده AI بتواند:

```text
User
 ↓
AI
 ↓
Intent
 ↓
Action Plan
 ↓
Actions
 ↓
Capabilities
 ↓
Termux
```

را طی کند.

مدل ممنوع:

```text
User
 ↓
AI
 ↓
Unrestricted Shell
```

بنابراین Capability Model یکی از پایه‌های امنیتی AI آینده خواهد بود، ولی AI جزو Scope فعلی نیست.

---

# 40. Capability Decision Matrix

| Capability | Status | Primary role | Likely tiers | Major dependency / concern |
|---|---|---|---|---|
| Filesystem | Core Candidate | File/resource operations | Visual / Guided / Terminal | Android/Termux filesystem boundary |
| Process | Core Candidate | Process execution/lifecycle | Guided / Terminal | Process control and observability |
| Network | Core Candidate | Network access | Visual / Guided / Terminal | Connectivity, timeout, security |
| Package | Core Candidate | Package management | Visual / Guided / Terminal | Package manager semantics |
| Git | Core Candidate | Version control | Visual / Guided / Terminal | Git availability and semantics |
| Terminal | Core Candidate | Full interactive CLI | Direct Terminal | PTY/session/transport validation |
| Project | Core Candidate | Project-level context/actions | Visual / Guided | Project model unresolved |
| Context | Core Candidate / Cross-cutting | Runtime semantic context | All | Context model unresolved |

---

# 41. What Is Not Yet a Capability Decision

موارد زیر هنوز تصمیم معماری نهایی نیستند:

```text
Exact API
Exact Kotlin interfaces
Exact database model
Exact session implementation
Exact PTY transport
Exact filesystem bridge
Exact permission API
Exact project metadata schema
Exact workspace model
Plugin runtime
AI provider
Performance budgets
minSdk
Exact package structure
Exact Compose architecture
```

این موارد عمداً خارج از قفل این سند باقی می‌مانند.

---

# 42. Validation Requirements

قبل از تبدیل Candidate Capabilityها به implementation contract باید موارد زیر validate شوند:

## Architecture validation

- [ ] Capability boundaries are non-overlapping enough.
- [ ] Capability dependencies do not create circular ownership.
- [ ] Actions can consume capabilities without depending on UI.
- [ ] Commands remain implementation details.

## Termux validation

- [ ] Execution mechanism is validated.
- [ ] Interactive terminal mechanism is validated.
- [ ] Session behavior is validated.
- [ ] Filesystem access mechanism is validated.
- [ ] Permission boundaries are validated.

## UX validation

- [ ] Capability can be exposed at the right interaction tier.
- [ ] Common Actions reduce cognitive load.
- [ ] Advanced users retain terminal escape hatches.
- [ ] Consequences remain visible for risky operations.

## Product validation

- [ ] Capability has real user value.
- [ ] Capability is reusable.
- [ ] Capability does not exist merely to justify a Feature.
- [ ] Capability does not create unnecessary Core complexity.

---

# 43. Architecture Drift Guards

در ادامه توسعه Atlas، هر طراحی جدید باید در برابر این موارد بررسی شود.

## Red Flag 1

```text
Button → execute("...")
```

نشانه وابستگی UI به command implementation.

---

## Red Flag 2

```text
Feature → private shell command format
```

نشانه command-centric architecture.

---

## Red Flag 3

```text
New Feature → New special bridge
```

اگر هر Feature bridge اختصاصی خود را بخواهد، Capability/Action architecture احتمالاً دور زده شده است.

---

## Red Flag 4

```text
Capability = Screen
```

Capability نباید با صفحه UI یکی فرض شود.

---

## Red Flag 5

```text
Capability = one CLI tool
```

یک CLI tool لزوماً Capability نیست.

مثلاً:

```text
ffmpeg
```

به‌خودی‌خود Capability محسوب نمی‌شود.

Capability بالاتر ممکن است:

```text
Media Processing
```

باشد، اگر و زمانی که semantics آن در Atlas ارزش abstraction داشته باشد.

---

## Red Flag 6

```text
Every possible Linux tool → Atlas GUI
```

این دقیقاً همان Feature Dump است که Capability Model باید جلوی آن را بگیرد.

---

# 44. Feature Admission Example — Clone Repository

برای نشان دادن نحوه استفاده از Capability Model:

```text
User Intent
    ↓
Clone Repository
    ↓
Action: CloneRepository
    ↓
Capabilities:
    Git
    Network
    Filesystem
    Process
    Context
    ↓
Validation
    ↓
Execution
    ↓
Result
```

UI می‌تواند:

```text
Clone Repository
 → URL
 → Destination
 → Clone
```

باشد.

کاربر نباید برای مسیر معمول مجبور به نوشتن:

```text
git clone ...
```

شود.

اما کاربر حرفه‌ای باید همچنان بتواند از Terminal استفاده کند.

---

# 45. Feature Admission Example — Rename File

```text
User Intent
    ↓
Rename File
    ↓
Action: RenameFile
    ↓
Capabilities:
    Filesystem
    Context
    ↓
Validation
    ↓
Preview if needed
    ↓
Execute
    ↓
Result
```

UI:

```text
Long press
 → Rename
 → New Name
 → Confirm
```

این Action نباید command string را به UI نشت دهد.

---

# 46. Feature Admission Example — Install Package

```text
User Intent
    ↓
Install Package
    ↓
Action: InstallPackage
    ↓
Capabilities:
    Package
    Process
    Context
    ↓
Availability validation
    ↓
Risk assessment
    ↓
Confirmation if required
    ↓
Execute
    ↓
Observable result
```

Implementation package manager هنوز به Termux Integration/Action Architecture وابسته است.

---

# 47. Feature Admission Example — Interactive Terminal

```text
User Intent
    ↓
Open Terminal
    ↓
Action: OpenTerminalSession
    ↓
Capabilities:
    Terminal
    Process
    Context
    ↓
Validated interactive transport
    ↓
Session
```

در اینجا Terminal خودِ abstraction است، نه صرفاً fallback.

---

# 48. Relationship to Action Architecture

این سند عمداً قبل از `03_Action_Architecture.md` قرار گرفته است.

Capability Model مشخص می‌کند:

```text
What the system can do
```

Action Architecture باید مشخص کند:

```text
How a semantic operation requests and uses those abilities
```

بنابراین ترتیب:

```text
Capability
    ↓
Action
    ↓
Integration
```

حفظ می‌شود.

---

# 49. Relationship to Termux Integration

Capability Model نباید به implementation خاص Termux وابسته شود.

اما Termux Integration باید نشان دهد:

```text
How each required capability is actually provided
```

مثلاً:

```text
Process Capability
    ↓
Termux execution mechanism

Terminal Capability
    ↓
Validated PTY/session mechanism

Filesystem Capability
    ↓
Validated file access mechanism
```

این mapping در:

`04_Termux_Integration.md`

تکمیل خواهد شد.

---

# 50. Relationship to Project / Workspace Model

Project و Context در Capability Model به‌عنوان Candidate باقی می‌مانند.

اما تعریف دقیق آنها deferred است.

ترتیب صحیح:

```text
Capability Model
    ↓
Action Architecture
    ↓
Termux Integration
    ↓
Project / Workspace Model
```

این ترتیب اجازه می‌دهد Project Model تحت فشار Featureهای زودهنگام طراحی نشود.

---

# 51. Current Architectural Verdict

در این مرحله، Capability Model به‌عنوان ستون معماری پیشنهادی Atlas پذیرفته می‌شود، اما هنوز Implementation Contract نیست.

وضعیت:

```text
Capability-oriented architecture
        ↓
        APPROVED AS DIRECTION

Core Capability Set
        ↓
        CANDIDATE

Capability Contract
        ↓
        DRAFT

Capability API
        ↓
        DEFERRED

Termux mapping
        ↓
        NEEDS VALIDATION

Permission mapping
        ↓
        NEEDS SPECIFICATION
```

---

# 52. Decisions

تصمیمات این سند:

1. Atlas architecture باید Capability-oriented باشد.
2. Featureها باید روی Capabilityها ساخته شوند.
3. Capability با Action یکسان نیست.
4. Action با Command یکسان نیست.
5. Command implementation است.
6. Terminal یک Capability/Interface first-class است.
7. Capabilityها نباید مستقیماً به UI وابسته باشند.
8. Capabilityها نباید implementation خاصی را به UX تحمیل کنند.
9. Capability composition باید از طریق Action/Execution orchestration انجام شود.
10. Capability discovery باید تا حد امکان validated باشد.
11. Lite و Pro باید روی architecture مشترک ساخته شوند.
12. AI و Plugin فعلاً خارج از implementation scope هستند.
13. Termux integration assumptions هنوز باید validate شوند.
14. Project/Workspace هنوز نهایی نشده‌اند.
15. Capability Model نباید به Feature Dump تبدیل شود.

---

# 53. Open Questions

این موارد باید در اسناد بعدی پاسخ داده شوند:

1. آیا `Context` واقعاً یک Capability است یا یک cross-cutting architectural service؟
2. آیا `Project` یک Capability است یا domain model + مجموعه‌ای از Actions؟
3. آیا `Network` باید یک Capability عمومی باشد یا چند Capability تخصصی؟
4. آیا `Process` باید مستقیماً توسط Actionها مصرف شود یا از طریق higher-level execution services؟
5. دقیقاً چه capabilityهایی در Core v1 باقی می‌مانند؟
6. Capability availability چگونه مدل می‌شود؟
7. Capability versioning چگونه انجام می‌شود؟
8. Permission/risk metadata دقیقاً در Capability قرار می‌گیرد یا Action؟
9. Capability composition چگونه در Action Contract نمایش داده می‌شود؟
10. چگونه Capabilityها در Termux environment discover و validate می‌شوند؟

این سؤال‌ها نباید با حدس پاسخ داده شوند.

---

# 54. Next Step

قدم بعدی رسمی:

```text
02_Capability_Model.md
        ↓
03_Action_Architecture.md
```

در `03_Action_Architecture.md` باید موارد زیر به قرارداد دقیق تبدیل شوند:

```text
Intent
Action
Action Contract
Input
Output
Validation
Execution
Result
Error
Progress
Cancellation
Recovery
Context
Destructive Actions
Action Composition
Capability declarations
```

پس از آن:

```text
04_Termux_Integration.md
```

باید Capabilityها و Actionها را به مکانیزم‌های واقعی Termux متصل و assumptions را validate کند.

---

# 55. Final Reference Model

مدل مرجع فعلی Atlas:

```text
                         ATLAS
                           │
                    Native Android UX
                           │
                      User Intent
                           │
                         Action
                           │
                 Required Capabilities
                           │
                    Action Execution
                           │
                    Termux Adapter
                           │
                     Termux / Linux
                           │
              ┌────────────┼────────────┐
              ↓            ↓            ↓
            Git          Python        SSH
              ↓            ↓            ↓
          Linux Tools   Linux Tools   Linux Tools
```

اصل نهایی:

> **Capabilities define what Atlas can meaningfully enable. Actions define what Atlas is doing. Commands define how the underlying system may implement it.**

و اصل UX:

> **GUI when possible. CLI when necessary.**

و اصل کنترل:

> **Hide syntax by default. Never hide consequences.**

---

# Document Status

**Status: Architecture Draft / Decision Candidate**

این سند باید قبل از تبدیل شدن به implementation contract با `03_Action_Architecture.md` و سپس `04_Termux_Integration.md` cross-check شود.

هر contradiction واقعی که در این cross-check پیدا شود باید صریحاً ثبت و resolution آن مشخص شود.

**End of Document**
