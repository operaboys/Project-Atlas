# Atlas — Action Architecture

> سند: `03_Action_Architecture.md`
> Version: `1.0-draft`
> Date: 2026-08-30
> Status: **Architecture Draft / Decision Candidate**
> Position: **03 — Action Architecture**
> Depends on: `01_Core_Philosophy.md`, `02_Capability_Model.md`
> Next: `04_Termux_Integration.md`

---

## 1. Purpose

این سند مدل معماری Action در Atlas را از سطح فلسفه به یک مدل مهندسی قابل استفاده منتقل می‌کند.

اصل مرکزی:
> **Command is implementation. Action is abstraction.**

هدف، جداکردن خواسته کاربر از syntax و implementation است و تعریف دقیق مرزهای Intent، Action، Capability، Execution، Result و Error.

این سند هنوز Kotlin/API implementation contract نهایی نیست و عمداً مکانیزم‌های Termux مانند PTY، Socket و Session API را قفل نمی‌کند.

---

## 2. Architectural Position

مدل مرجع:

```text
Atlas UI
    ↓
User Intent
    ↓
Intent Resolution
    ↓
Action
    ↓
Required Capabilities + Context
    ↓
Validation
    ↓
Execution
    ↓
Adapter
    ↓
Termux / Linux
    ↓
Result / Events
    ↓
Atlas UI
```

Action مرز معنایی بین «چه کاری باید انجام شود» و «چگونه در محیط زیرین اجرا شود» است.

---

## 3. Intent

Intent بیان‌کننده هدف یا خواسته کاربر است.

نمونه:
```text
"می‌خواهم این repository را دریافت کنم."
```

Intent می‌تواند از GUI، Command Palette و در آینده AI یا Automation بیاید.

Intent الزاماً برابر Action نیست؛ resolution می‌تواند context و اطلاعات تکمیلی را وارد کند و سپس Action مناسب را انتخاب کند.

---

## 4. Intent Resolution

مدل:

```text
Intent
  ↓
Context
  ↓
Resolution
  ↓
Action
```

اگر اطلاعات لازم ناقص یا مبهم باشد:

```text
Intent
  ↓
Missing Information
  ↓
Collect / Ask
  ↓
Resolved Intent
  ↓
Action
```

Resolution نباید کاربر را مجبور به فکرکردن با syntax command کند.

---

## 5. Action Definition

در Atlas:

> **Action یک عملیات معنایی، مشخص و قابل اجراست که یک هدف مشخص را با استفاده از یک یا چند Capability تحقق می‌دهد.**

نمونه‌ها:

```text
CreateProject
CloneRepository
RenameFile
InstallPackage
RunScript
OpenTerminalSession
```

Action باید semantic باشد، نه command-centric.

---

## 6. Action Boundaries

Action باید:
- هدف معنایی مستقل داشته باشد.
- Input مشخص داشته باشد.
- Preconditions و validation مشخص داشته باشد.
- Capabilityهای موردنیاز را اعلام کند.
- lifecycle قابل مشاهده داشته باشد.
- Result و Error قابل مدل‌سازی داشته باشد.
- cancellation/recovery را در صورت امکان مشخص کند.
- risk/destructive characteristics را قابل ارزیابی کند.

Action نباید:
- Compose component یا Screen باشد.
- command string باشد.
- مستقیماً UI state را مدیریت کند.
- implementation یک CLI tool باشد.
- به یک bridge خاص Termux در contract خود قفل شود.

---

## 7. Intent vs Action vs Capability vs Command vs Feature

این پنج مفهوم باید جدا بمانند.

```text
Intent
= هدف کاربر

Action
= عملیات معنایی Atlas

Capability
= توانمندی موردنیاز سیستم

Command
= یکی از implementationهای ممکن

Feature
= تجربه محصولی قابل مشاهده برای کاربر
```

مثال:

```text
Intent: "این repository را بیاور"
Action: CloneRepository
Capabilities: Git + Network + Filesystem + Process + Context
Command: git clone ...       ← implementation
Feature: Git workflow        ← product experience
```

هیچ Feature نباید مستقیماً contract خود را به command string تبدیل کند.

---

## 8. Action Contract

Action Contract در سطح مفهومی شامل این اجزاست:

```text
Identity
Intent meaning
Input
Context requirements
Capability requirements
Preconditions
Validation
Execution semantics
Result
Error model
Progress
Events / observability
Cancellation
Recovery
Retryability
Timeout policy
Risk / destructive classification
Postconditions
```

این ساختار **Candidate Contract** است؛ API نهایی بعداً تعیین می‌شود.

---

## 9. Action Identity

هر Action باید identity پایدار و غیرمبهم داشته باشد.

نمونه:

```text
atlas.files.rename
atlas.git.clone
atlas.package.install
atlas.process.run
atlas.terminal.open
```

namespace و versioning نهایی هنوز تصمیم نشده‌اند.

اصل ثابت:
> Action identity نباید به command syntax وابسته باشد.

---

## 10. Input Model

Input باید semantic باشد.

مدل مطلوب:

```text
CloneRepositoryInput
    repositoryUrl
    destination
    branch?
```

مدل نامطلوب:

```text
command = "git clone ... --branch ..."
```

UI می‌تواند Input را جمع کند؛ Action باید correctness آن را مستقل از UI بررسی کند.

---

## 11. Validation

Validation سه سطح دارد:

```text
UI / UX validation
        ↓
Action validation
        ↓
Runtime / environment validation
```

UI validation برای تجربه کاربر است.

Action validation برای correctness است.

Runtime validation برای واقعیت محیط است؛ مثلاً اینکه Git واقعاً در محیط موجود و قابل اجرا باشد.

هیچ validation مهمی نباید فقط در UI وجود داشته باشد.

---

## 12. Preconditions and Postconditions

Action می‌تواند precondition داشته باشد:

```text
CommitChanges
    repository exists
    Git available
    changes exist
```

و postcondition:

```text
RenameFile
    old path absent
    new path present
```

برای Actionهای حساس، verification بعد از execution می‌تواند لازم باشد.

```text
Execute
  ↓
Verify
  ↓
Result
```

---

## 13. Capability Requirements

هر Action باید بتواند Capabilityهای موردنیاز خود را اعلام کند.

مثال:

```text
CloneRepository
    Git
    Network
    Filesystem
    Process
    Context
```

این declaration به معنی وابستگی به implementation نیست.

Action می‌گوید «به Git نیاز دارم»، نه «باید command X را اجرا کنم». 

---

## 14. Capability Availability

وجود Capability با availability آن یکی نیست:

```text
Capability exists
        ≠
Capability available now
```

نمونه:

```text
Git
    Present
    Missing
    Incompatible
    Unknown
```

Action قبل از execution باید بتواند وضعیت موردنیاز را دریافت کند.

Discovery mechanism در `04_Termux_Integration.md` مشخص خواهد شد.

---

## 15. Context

Action ممکن است به Context نیاز داشته باشد.

Contextهای فعلی Candidate:

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

Action نباید به UI navigation state وابسته شود.

اگر context مبهم باشد، پرسیدن از کاربر از حدس‌زدن بهتر است.

---

## 16. Explicit and Implicit Context

Context می‌تواند explicit باشد:

```text
RenameFile(file = X)
```

یا از context جاری resolve شود:

```text
Rename current file
```

برای عملیات مهم، implicit context فقط وقتی مجاز است که ambiguity کنترل شده باشد.

---

## 17. Lifecycle

Lifecycle پیشنهادی:

```text
Created
   ↓
Validated
   ↓
Queued
   ↓
Running
   ↓
Completed
```

مسیرهای دیگر:

```text
Validated → Failed
Queued → Cancelled
Running → Failed
Running → Cancelled
Running → Timed Out
Running → Unknown
```

در صورت امکان:

```text
Failed → Retry
Completed → Recovery
```

---

## 18. Lifecycle Semantics

`Created`: Action ساخته شده است.

`Validated`: ورودی و preconditionهای لازم بررسی شده‌اند.

`Queued`: برای execution پذیرفته شده و منتظر اجراست.

`Running`: execution فعال است.

`Completed`: هدف مورد انتظار تحقق یافته است.

`Partial Success`: بخشی موفق و بخشی ناموفق است.

`Warning`: نتیجه قابل قبول است ولی نیازمند توجه است.

`Failed`: هدف محقق نشده است.

`Cancelled`: execution متوقف شده است.

`Timed Out`: در محدوده زمانی مجاز کامل نشده است.

`Unknown`: وضعیت نهایی با evidence کافی قابل تعیین نیست.

---

## 19. Result Model

Result باید semantic باشد:

```text
ActionResult
    status
    data
    summary
    warnings
    output
    diagnostics
    metadata
```

ساختار دقیق API هنوز deferred است.

Raw stdout به‌تنهایی Result نیست.

---

## 20. Semantic Result vs Raw Output

مدل نامطلوب:

```text
stdout = "..."
```

مدل مطلوب:

```text
semantic result
+
raw output when useful
+
diagnostics
```

UI معمولی نباید مجبور باشد stdout را parse کند.

کاربر حرفه‌ای باید بتواند جزئیات فنی را مشاهده کند.

---

## 21. Error Model

Taxonomy پیشنهادی:

```text
InvalidInput
PermissionDenied
CapabilityUnavailable
ToolMissing
NetworkFailure
FilesystemFailure
ProcessFailure
IntegrationFailure
Conflict
Cancelled
TimedOut
Unknown
```

این taxonomy هنوز Candidate است.

هر Error باید تا حد امکان دو لایه داشته باشد:

```text
Human-readable explanation
+
Technical diagnostics
```

---

## 22. Error Contract

Error مفهومی می‌تواند شامل این موارد باشد:

```text
category
message
userMessage
technicalDetails
recoverability
retryability
source
```

Action نباید error را فقط به raw CLI text محدود کند.

---

## 23. Progress

Actionهای طولانی باید در صورت امکان progress ارائه کنند:

```text
currentStep
totalSteps?
completedSteps?
percentage?
message
```

Progress می‌تواند determinate یا indeterminate باشد.

نباید درصد جعلی تولید شود.

---

## 24. Action Events

Eventهای semantic نمونه:

```text
ActionStarted
StepStarted
ProgressUpdated
OutputReceived
WarningRaised
StepCompleted
ActionCompleted
ActionFailed
ActionCancelled
```

Event نباید صرفاً mirror خام stdout باشد.

---

## 25. Cancellation

Cancellation باید first-class concern باشد.

صرفاً داشتن:

```text
supportsCancellation = true
```

کافی نیست.

باید مشخص باشد:

```text
Can request cancellation?
Can execution actually stop?
What happens to partial state?
What result is returned?
```

Cancellation request لزوماً توقف فوری process نیست.

---

## 26. Cancellation Semantics

مدل:

```text
User
 ↓
Cancel Request
 ↓
Action
 ↓
Execution Adapter
 ↓
Underlying process
```

در صورت امکان:

```text
Cancellation requested
        ↓
Graceful stop
        ↓
Force stop if necessary
```

signal، PTY و process semantics در Integration Specification تعیین می‌شوند.

---

## 27. Recovery

Recovery با Retry یکسان نیست.

Retry:
```text
Run the same operation again
```

Recovery:
```text
Restore a safe or expected state
```

Recovery باید فقط وقتی اعلام شود که واقعاً قابل انجام باشد.

اگر امکان بازگردانی وجود ندارد:

```text
Recovery = Unsupported
```

نباید fake undo ایجاد شود.

---

## 28. Dry Run and Preview

برخی Actionها ممکن است dry-run یا preview داشته باشند:

```text
supportsDryRun
```

اما فقط وقتی semantics واقعی و قابل اعتماد وجود دارد.

اگر underlying tool dry-run ندارد، simulation ناقص نباید به‌عنوان dry-run قطعی ارائه شود.

---

## 29. Destructive Actions and Risk

نمونه:

```text
DeleteFile
DeleteDirectory
PackageRemove
GitReset
GitClean
ProcessTerminate
```

Risk classification پیشنهادی:

```text
Low
Medium
High
Critical
```

threshold نهایی در Security Model تعیین خواهد شد.

---

## 30. Confirmation

Confirmation نباید برای همه عملیات یکسان باشد.

اصل:

```text
Low risk
    → minimal friction

High risk
    → explicit confirmation

Critical / irreversible
    → stronger guard
```

هدف:

> **Minimum Cognitive Load without hiding consequences.**

---

## 31. Execution Boundary

Action نباید transport را مدیریت کند.

مدل:

```text
Action
 ↓
Execution Request
 ↓
Execution Layer
 ↓
Adapter
 ↓
Termux
```

Action مشخص می‌کند:

```text
what
```

Execution/Adapter مشخص می‌کند:

```text
how
```

این مرز برای تست‌پذیری و جلوگیری از command-centric design حیاتی است.

---

## 32. Command Generation

اگر Action نهایتاً با CLI اجرا شود:

```text
Action
 ↓
Implementation
 ↓
Command construction
 ↓
Execution Adapter
```

Command construction نباید در UI باشد.

Command string contract پایدار Action نیست.

---

## 33. Command Visibility

برای transparency، در سطح Expert می‌توان:

```text
View operation details
View generated command
Open Terminal
```

را ارائه کرد.

Command نمایش‌داده‌شده implementation detail است، نه abstraction اصلی Atlas.

---

## 34. Action Composition

Actionهای پیچیده می‌توانند چند Action را orchestrate کنند:

```text
CreatePythonProject
    ↓
CreateDirectory
    ↓
CreateEnvironment
    ↓
InstallDependencies
    ↓
FinalizeProject
```

هر function داخلی نباید Action عمومی شود.

Composition باید deliberate باشد.

---

## 35. Composite Action

Composite Action باید:

- dependencyهای خود را مشخص کند.
- state کلی را گزارش کند.
- failure propagation داشته باشد.
- cancellation semantics داشته باشد.
- recovery strategy را مشخص کند.
- partial success را قابل گزارش کند.

---

## 36. Failure Propagation

اگر:

```text
A → success
B → success
C → failed
```

نتیجه نباید صرفاً `Failed` باشد.

در صورت نیاز:

```text
Partial Success
+
failed step
+
completed steps
+
recovery information
```

باید قابل گزارش باشد.

---

## 37. Atomicity

همه Actionها atomic نیستند.

Candidate states:

```text
Atomic
Partially atomic
Non-atomic
Unknown
```

Atomicity برای retry، cancellation و recovery اهمیت دارد.

---

## 38. Idempotency

Action در صورت امکان باید رفتار اجرای مجدد را مشخص کند.

Idempotency برای:

```text
Retry
Automation
Recovery
```

مهم است.

این metadata هنوز بخشی از final API نیست.

---

## 39. Retry

Retry فقط برای failureهای retryable مناسب است.

مثلاً:

```text
temporary network failure
```

ممکن است retryable باشد.

اما:

```text
invalid input
```

نیست.

Retry policy نهایی هنوز open است.

---

## 40. Timeout

Timeout باید action-aware باشد، نه یک عدد arbitrary جهانی.

مدل:

```text
Action
 ↓
Timeout Policy
 ↓
Execution
```

پس از timeout ممکن است نتیجه:

```text
Timed Out
```

یا در شرایطی:

```text
Unknown
```

باشد.

---

## 41. Unknown State

اگر ارتباط با execution backend قطع شود، Atlas نباید بدون evidence نتیجه را `Failed` یا `Completed` اعلام کند.

```text
Unknown
```

یک state واقعی و مهم است.

این موضوع برای correctness، safety و transparency حیاتی است.

---

## 42. Verification

برای Actionهای حساس، exit code به‌تنهایی همیشه کافی نیست.

مدل:

```text
Execute
 ↓
Verify postcondition
 ↓
Result
```

Verification باید متناسب با اهمیت operation و هزینه آن استفاده شود.

---

## 43. Action Availability

Action می‌تواند از نظر semantic وجود داشته باشد ولی در محیط فعلی unavailable باشد.

```text
Action exists
      ≠
Action executable now
```

UI باید این تفاوت را قابل فهم نمایش دهد.

---

## 44. Multiple Interfaces

هدف Action Layer این است که یک Action از چند interface قابل استفاده باشد:

```text
GUI
 ↓
Action

Future AI
 ↓
Action

Future Automation
 ↓
Action
```

Terminal همچنان first-class است و نباید به یک fallback بی‌ارزش تبدیل شود.

---

## 45. UI Independence

Action باید بدون Compose قابل تست باشد.

مدل مطلوب:

```text
Compose
   ↓
Intent
   ↓
Action
```

نه:

```text
Compose
   ↓
ViewModel-specific shell command
```

Business logic نباید به UI component وابسته باشد.

---

## 46. Testing Model

چهار سطح مهم:

```text
Unit
    validation / result / error / state

Contract
    capabilities / preconditions / postconditions / cancellation

Integration
    Action → Adapter → Termux

End-to-End
    UI → Intent → Action → Termux → Result → UI
```

Integration و E2E نیازمند محیط واقعی و validation هستند.

---

## 47. Test Execution Boundary

برای تست بدون Termux واقعی، execution boundary باید قابل جایگزینی باشد:

```text
Action
 ↓
Execution Interface
 ├── Real Termux Adapter
 └── Fake/Test Adapter
```

این یک architectural direction است، نه API نهایی.

---

## 48. Action Granularity

Action بیش از حد بزرگ:

```text
DevelopEntireApplication
```

بیش از حد مبهم است.

Action بیش از حد کوچک:

```text
AllocateOneInternalBuffer
```

implementation-centric است.

سطح مناسب معمولاً semantic و دارای ارزش مستقل است:

```text
CreateProject
CloneRepository
RenameFile
InstallPackage
RunScript
```

---

## 49. Workflow vs Action

Workflow مجموعه‌ای از عملیات است.

اگر یک operation semantic identity مستقل دارد، Action مستقل مناسب‌تر است.

اگر صرفاً orchestration چند operation است، Composite Action/Workflow مناسب‌تر است.

---

## 50. Anti-Patterns

موارد زیر Red Flag هستند:

```text
UI → shell command
Action → Compose state
Action → hard-coded Termux bridge
Every CLI command = Action
Every internal function = Action
Error = raw stdout/stderr
Timeout = automatic Failed
Cancellation = pretend stopped
```

همچنین:

```text
Feature → private command format
New Feature → new special bridge
```

نشانه architecture drift هستند.

---

## 51. Action Architecture Gate

قبل از implementation یک Action مهم:

```text
Intent defined
    ↓
Semantic Action defined
    ↓
Inputs defined
    ↓
Capabilities declared
    ↓
Preconditions defined
    ↓
Result defined
    ↓
Errors defined
    ↓
Cancellation considered
    ↓
Recovery considered
    ↓
Risk assessed
    ↓
Execution boundary defined
```

اگر این زنجیره ناقص باشد، implementation باید متوقف یا محدود شود.

---

## 52. Example — RenameFile

```text
Intent
    Rename File

Action
    RenameFile

Input
    source
    newName

Capabilities
    Filesystem
    Context

Validation
    source exists
    name valid
    conflict policy

Risk
    Medium candidate

Execution
    filesystem rename

Result
    renamed resource

Recovery
    depends on filesystem semantics
```

---

## 53. Example — CloneRepository

```text
Intent
    Clone Repository

Action
    CloneRepository

Input
    repositoryUrl
    destination
    branch?

Capabilities
    Git
    Network
    Filesystem
    Process
    Context

Validation
    URL
    destination
    availability
    conflict policy

Execution
    Git implementation through adapter

Progress
    only if meaningful progress is available

Result
    repository/resource information

Errors
    Git / Network / Filesystem / Permission / Process

Recovery
    partial clone cleanup when safely possible
```

---

## 54. Example — InstallPackage

```text
Action
    InstallPackage

Input
    packageIdentifier

Capabilities
    Package
    Process
    Context

Validation
    identifier
    capability availability

Risk
    Medium / policy dependent

Execution
    underlying package manager

Result
    package state

Cancellation
    depends on underlying execution semantics
```

---

## 55. Example — OpenTerminalSession

```text
Action
    OpenTerminalSession

Input
    shell?
    workingDirectory?
    environment?

Capabilities
    Terminal
    Process
    Context

Execution
    validated interactive transport

Result
    terminal session state/handle

Important
    PTY/session mechanism is not implementation-decided here.
```

---

## 56. Security Boundary

مدل مفهومی:

```text
Intent
 ↓
Action
 ↓
Risk
 ↓
Permission / Confirmation
 ↓
Capability
 ↓
Execution
```

AI آینده نیز باید همین مسیر را طی کند.

مدل ممنوع:

```text
AI → unrestricted shell
```

---

## 57. Permission Ownership — Open Decision

هنوز تصمیم نهایی نشده که risk/permission metadata دقیقاً در Action، Capability یا هر دو قرار گیرد.

Proposal:

```text
Capability
    → intrinsic capability risk

Action
    → operation-specific risk
```

مثلاً Filesystem یک capability است، اما DeleteDirectory ریسک operation-specific دارد.

این Proposal است، نه final contract.

---

## 58. Action Registry — Deferred

ممکن است در آینده registry لازم شود:

```text
Action ID
Description
Input schema
Required capabilities
Availability
Risk
Supported interfaces
```

اما implementation registry فعلاً نباید ساخته شود.

ابتدا semantics، lifecycle، security و integration باید تثبیت شوند.

---

## 59. Relationship to Capability Model

Capability Model پاسخ می‌دهد:

> **What Atlas can meaningfully enable.**

Action Architecture پاسخ می‌دهد:

> **What Atlas is doing and how that operation is modeled.**

رابطه:

```text
Capability
    ↓
Action
    ↓
Execution
```

Action می‌تواند چند Capability مصرف کند؛ Capability نباید orchestration همه Actionها را مالک شود.

---

## 60. Relationship to Termux Integration

این سند مکانیزم Termux را قفل نمی‌کند.

`04_Termux_Integration.md` باید مشخص کند:

```text
Action
 ↓
Execution
 ↓
Termux Adapter
 ↓
Actual mechanism
```

مواردی مانند PTY، Socket، Quick Exec، Session API و FileProvider در این سند فقط concernهای integration هستند، نه decision نهایی.

---

## 61. Relationship to Project / Workspace

Action می‌تواند Context دریافت کند، اما Project/Workspace model نباید زودتر از سند 14 قفل شود.

جزئیات Project identity، root path، metadata و workspace lifecycle در:

`05_Project_Workspace.md`

تعیین خواهد شد.

---

## 62. Relationship to AI

AI فعلاً Deferred است.

مدل آینده:

```text
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
Execution
```

AI نباید bypass Action/Security architecture کند.

---

## 63. Relationship to Plugin System

Plugin نیز Deferred است.

اگر در آینده Plugin بتواند Action ارائه کند:

```text
Plugin
 ↓
Action / Extension API
 ↓
Capabilities
```

نه:

```text
Plugin
 ↓
Atlas internals
```

---

## 64. Decisions

تصمیمات این نسخه:

1. Action abstraction اصلی عملیات معنایی Atlas است.
2. Intent و Action جدا هستند.
3. Command implementation است، نه contract.
4. UI نباید مستقیماً command اجرا کند.
5. Action باید semantic identity داشته باشد.
6. Action باید input و result مشخص داشته باشد.
7. Validation نباید فقط در UI باشد.
8. Capability requirements باید قابل declaration باشند.
9. Lifecycle باید observable باشد.
10. Result باید semantic باشد و raw output auxiliary.
11. Error باید structured و human-readable + technical باشد.
12. Cancellation first-class است.
13. Recovery باید واقعی و صادقانه باشد.
14. Dry run فقط با semantics معتبر مجاز است.
15. Destructive actions باید risk-aware باشند.
16. Unknown state باید حفظ شود، نه اینکه حدس زده شود.
17. Composite Actions برای orchestration قابل استفاده‌اند.
18. Action باید از UI مستقل باشد.
19. Execution mechanism پشت adapter boundary قرار می‌گیرد.
20. PTY/Socket/Termux mechanism فعلاً قفل نمی‌شود.
21. AI و Plugin فعلاً implementation scope نیستند.
22. Action API نهایی هنوز deferred است.

---

## 65. Open Questions

1. Intent دقیقاً در کدام layer resolve می‌شود؟
2. Intent Resolution service مستقل است یا Core component؟
3. آیا Action Registry لازم است؟
4. Action identity چگونه versioned می‌شود؟
5. Input/Output schema دقیقاً چگونه تعریف می‌شود؟
6. Capability requirements چگونه encode می‌شوند؟
7. Permission/risk metadata در Action، Capability یا هر دو؟
8. Result schema دقیق چیست؟
9. Event stream چگونه مدل می‌شود؟
10. Cancellation control چگونه طراحی می‌شود؟
11. Recovery چگونه استاندارد می‌شود؟
12. Composite Action و Workflow دقیقاً چه تفاوتی دارند؟
13. Retry policy کجا قرار می‌گیرد؟
14. Timeout policy کجا قرار می‌گیرد؟
15. Unknown state چگونه persist/resolve می‌شود؟
16. Execution concurrency چگونه مدیریت می‌شود؟
17. آیا Actionها queue می‌شوند؟
18. آیا Action می‌تواند Action دیگری را مستقیم invoke کند؟
19. Context resolution چه lifecycleای دارد؟
20. Action backward compatibility چگونه مدیریت می‌شود؟

این موارد نباید با حدس نهایی شوند.

---

## 66. Validation Gates

قبل از implementation contract:

### Architecture
- [ ] Intent/Action boundary validated.
- [ ] Action/Capability boundary validated.
- [ ] Action/Execution boundary validated.
- [ ] No UI dependency.
- [ ] No command-centric contract.

### Lifecycle
- [ ] State machine validated.
- [ ] Cancellation semantics validated.
- [ ] Unknown state behavior validated.
- [ ] Retry/timeout semantics validated.

### Result/Error
- [ ] Result schema validated.
- [ ] Error taxonomy validated.
- [ ] Progress model validated.
- [ ] Event/stream model validated.

### Security
- [ ] Risk ownership validated.
- [ ] Permission boundary validated.
- [ ] Destructive action policy validated.

### Integration
- [ ] Termux execution boundary validated.
- [ ] Interactive execution boundary validated.
- [ ] Filesystem boundary validated.

---

## 67. Current Architectural Verdict

```text
Action-oriented architecture
        ↓
        APPROVED AS DIRECTION

Semantic Action model
        ↓
        APPROVED AS DRAFT

Lifecycle
        ↓
        PROPOSED

Result/Error model
        ↓
        PROPOSED

Execution adapter boundary
        ↓
        APPROVED AS DIRECTION

Permission metadata placement
        ↓
        OPEN

Termux transport
        ↓
        NEEDS VALIDATION

Final API
        ↓
        DEFERRED
```

این سند Implementation Contract نهایی نیست.

---

## 68. Final Reference Model

```text
                         ATLAS
                           │
                    Native Android UX
                           │
                       User Intent
                           │
                    Intent Resolution
                           │
                         Action
                           │
             ┌─────────────┼─────────────┐
             ↓             ↓             ↓
       Validation      Capabilities    Context
             │             │             │
             └─────────────┼─────────────┘
                           ↓
                       Execution
                           ↓
                    Termux Adapter
                           ↓
                     Termux / Linux
                           ↓
                    CLI / OS Tools
                           ↓
                    Result / Events
                           ↓
                         Atlas UI
```

اصل معماری:

> **Intent says what the user wants. Action says what Atlas is doing. Capability says what Atlas needs. Command says how the underlying system may implement it.**

اصل UX:

> **GUI when possible. CLI when necessary.**

اصل شفافیت:

> **Hide syntax by default. Never hide consequences.**

اصل correctness:

> **When certainty is unavailable, report Unknown rather than inventing success or failure.**

---

## Document Status

**Architecture Draft / Decision Candidate**

این سند باید قبل از implementation نهایی با:

```text
02_Capability_Model.md
04_Termux_Integration.md
05_Project_Workspace.md
```

cross-check شود.

هر contradiction واقعی باید صریحاً ثبت و resolution آن مشخص شود.

**End of Document**