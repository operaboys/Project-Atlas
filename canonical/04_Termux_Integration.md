# Atlas — Termux Integration Specification

> سند: `04_Termux_Integration.md`
> Version: `1.0-draft`
> Date: `2026-08-30`
> Status: **Technical Architecture Draft / Validation Required**
> Position: **04 — Termux Integration**
> Depends on: `01_Core_Philosophy.md`, `02_Capability_Model.md`, `03_Action_Architecture.md`
> Next: `05_Project_Workspace.md`

---

# 1. Purpose

این سند مرز فنی بین Atlas و Termux/Linux را تعریف می‌کند.

هدف این سند پاسخ‌دادن به سؤال زیر است:

> **Atlas چگونه باید از قدرت Termux/Linux استفاده کند، بدون اینکه abstraction اصلی خود را به یک مکانیزم خاص Termux قفل کند؟**

اصل مرجع:

```text
Atlas
 ↓
Intent
 ↓
Action
 ↓
Capability
 ↓
Execution Boundary
 ↓
Termux Adapter
 ↓
Termux / Linux
```

این سند Implementation Guide نهایی نیست.

هر مکانیزمی که در این سند به‌عنوان Candidate، Validated یا Rejected معرفی شده باید بر اساس evidence واقعی، source code و prototype قابل بازبینی باشد.

---

# 2. Architectural Principle

Atlas جایگزین Termux نیست.

Termux backend و execution environment است.

مدل مطلوب:

```text
Atlas = Native Android UX / Graphical Shell
Termux = Linux execution environment
```

Atlas نباید:

```text
Termux semantics
```

را بی‌دلیل بازتعریف کند.

Atlas باید complexity غیرضروری را در UX پنهان کند، اما قدرت و escape hatch واقعی را حفظ کند.

---

# 3. Integration Boundary

مرز رسمی پیشنهادی:

```text
┌─────────────────────────────┐
│            Atlas            │
│                             │
│ Intent                      │
│ Action                      │
│ Capability                  │
│ Context                     │
│ Result / Error              │
└──────────────┬──────────────┘
               │
        Execution Contract
               │
┌──────────────▼──────────────┐
│      Termux Adapter         │
│                             │
│ command execution           │
│ interactive execution       │
│ result transport            │
│ capability discovery        │
│ integration diagnostics     │
└──────────────┬──────────────┘
               │
        Android IPC / API
               │
┌──────────────▼──────────────┐
│           Termux            │
│                             │
│ TermuxService               │
│ RunCommandService           │
│ terminal/session machinery  │
│ Termux filesystem           │
└──────────────┬──────────────┘
               │
        Linux userspace
               │
┌──────────────▼──────────────┐
│ CLI tools / processes       │
└─────────────────────────────┘
```

این boundary باید مستقل از UI باشد.

---

# 4. Current Evidence

بررسی منابع رسمی Termux نشان می‌دهد که Termux برای third-party apps یک مسیر رسمی برای اجرای command دارد: `RUN_COMMAND` Intent و `RunCommandService`.

در مستندات فعلی، این مسیر از Termux `0.95` به بعد برای third-party apps وجود دارد. اجرای command از طریق Java ممکن است result را نیز برگرداند، و برای استفاده نیاز به permission مربوط به `RUN_COMMAND` و فعال بودن `allow-external-apps` دارد. citeturn0search1turn1search1

Source فعلی `RunCommandService` نیز نشان می‌دهد که این سرویس execution request را دریافت کرده و آن را به `TermuxService` برای execution واقعی forward می‌کند. citeturn0search0turn1search7

نتیجه معماری:

```text
Atlas
 ↓
Termux RUN_COMMAND boundary
 ↓
TermuxService
 ↓
Process / Terminal execution
```

این یک integration mechanism معتبر برای بررسی و prototype است؛ اما به‌تنهایی کل Integration Architecture Atlas نیست.

---

# 5. RUN_COMMAND Capability

RUN_COMMAND را نباید به‌عنوان Capability سطح بالای Atlas تعریف کرد.

سطح درست:

```text
Action
    ↓
Process Capability
    ↓
Termux Execution Adapter
    ↓
RUN_COMMAND mechanism
```

مثلاً:

```text
RunScript
```

یک Action است.

```text
Process
```

Capability است.

```text
RUN_COMMAND
```

یک Termux integration mechanism است.

این distinction باید invariant باقی بماند.

---

# 6. RUN_COMMAND Inputs

منابع رسمی فعلی برای `RUN_COMMAND` ورودی‌هایی مانند اینها را پشتیبانی می‌کنند:

```text
command path
arguments
stdin
working directory
runner/background mode
session action
shell name
shell creation mode
command label
description
help
pending intent for results
result directory configuration
```

مستندات رسمی همچنین تصریح می‌کنند که `command path` ورودی اصلی است و `working directory` قابل تعیین است. citeturn0search1turn0search0

برای Atlas این mapping باید semantic باشد:

```text
Action Input
    ↓
Execution Request
    ↓
RUN_COMMAND fields
```

نه:

```text
UI
 ↓
raw Intent extras
```

---

# 7. RUN_COMMAND Security Preconditions

استفاده از RUN_COMMAND یک boundary امنیتی واقعی دارد.

بر اساس مستندات رسمی:

```text
com.termux.permission.RUN_COMMAND
```

باید توسط caller درخواست شود و به آن اجازه داده شود.

همچنین:

```text
allow-external-apps=true
```

باید در Termux تنظیم شده باشد.

برای Android target SDK 30+ نیز package visibility مربوط به `com.termux` باید رعایت شود. citeturn1search0turn1search1turn1search2

بنابراین Atlas نباید این prerequisites را silently assume کند.

Integration state باید بتواند مثلاً این وضعیت‌ها را تشخیص دهد:

```text
TermuxMissing
TermuxVersionUnsupported
RUN_COMMANDPermissionMissing
ExternalAppsDisabled
PackageVisibilityBlocked
Ready
Unknown
```

این taxonomy Candidate است.

---

# 8. User Consent and Trust

RUN_COMMAND عملاً امکان اجرای command در context ترموکس را در اختیار caller قرار می‌دهد.

خود Termux نیز این را یک boundary امنیتی می‌داند و permission و `allow-external-apps` را به‌عنوان کنترل استفاده می‌کند. citeturn1search0turn1search3

Source فعلی همچنین نشان می‌دهد که در صورت نقض policy مربوط به external apps، execution می‌تواند fail شود و notification/diagnostic به کاربر ارائه شود. citeturn1search7

Atlas باید این boundary را به security model خود منتقل کند، نه اینکه آن را یک technical nuisance تلقی کند.

---

# 9. Result Transport

مستندات RUN_COMMAND نشان می‌دهند که Java caller می‌تواند با `PendingIntent` result دریافت کند.

Result bundle می‌تواند شامل اطلاعاتی مانند:

```text
stdout
stderr
exitCode
error code
error message
execution id
```

باشد. citeturn1search1turn1search5

این برای Action Architecture مهم است:

```text
Termux Raw Result
       ↓
Termux Adapter
       ↓
Atlas Result/Error
```

Raw result نباید مستقیماً UI contract شود.

---

# 10. Result Size Constraints

RUN_COMMAND از Android Intent transport استفاده می‌کند.

مستندات رسمی هشدار می‌دهند که Android Intent extras محدودیت اندازه دارند و مجموع extras نباید از حدود 500KB عبور کند؛ همچنین برای arguments/command نیز محدودیت‌های اندازه وجود دارد. citeturn0search1turn0search2

نتیجه معماری:

```text
Small control data
    → Intent is reasonable

Large output / file / artifact
    → do not assume Intent payload
```

برای outputهای بزرگ باید mechanism جداگانه‌ای مثل file-backed result یا stream/transport مناسب بررسی شود.

این موضوع هنوز implementation decision نهایی نیست.

---

# 11. Background vs Terminal Session

RUN_COMMAND می‌تواند execution را در background یا terminal session انجام دهد و برای foreground command، session action نیز قابل تعیین است. citeturn0search1turn0search0

بنابراین Atlas حداقل دو semantic execution mode دارد:

```text
NonInteractive / Background
Interactive / Terminal
```

این دو نباید در Action Layer یکی فرض شوند.

مثال:

```text
InstallPackage
    → Background / noninteractive candidate

OpenTerminalSession
    → Interactive candidate

RunScript
    → either, depending on action contract
```

---

# 12. Interactive Terminal

Terminal در Atlas باید first-class باقی بماند.

Termux source فعلی `TerminalSession` را به‌عنوان یک terminal session متصل به یک process تعریف می‌کند و session دارای process I/O، terminal emulation، resize و process lifecycle است. citeturn0search4

این evidence نشان می‌دهد که terminal/session یک concern واقعی و مستقل از simple command execution است.

مدل:

```text
OpenTerminalSession
        ↓
Interactive Execution
        ↓
Terminal Session
        ↓
Process
```

---

# 13. PTY

PTY باید به‌عنوان technical concern در interactive execution بررسی شود.

اما این سند نمی‌گوید:

```text
Atlas → directly owns Termux PTY
```

مدل صحیح:

```text
Atlas Interactive Action
        ↓
Interactive Execution Contract
        ↓
Termux Adapter
        ↓
validated terminal/session mechanism
```

اگر Termux-provided terminal libraries یا session machinery برای use case Atlas کافی باشند، direct PTY ownership ممکن است غیرضروری باشد.

اگر کافی نباشند، prototype باید نیاز واقعی را اثبات کند.

---

# 14. Termux Libraries

مستندات Termux اعلام می‌کنند که repository رسمی سه library منتشر می‌کند:

```text
termux-shared
terminal-view
terminal-emulator
```

برای third-party apps، `termux-shared` برای تعامل با `termux-app` و constants/utilities معرفی شده و در صورت نیاز به terminal ارائه‌شده توسط Termux، `terminal-view` نیز مطرح است. citeturn1search5turn1search8

این به این معنی نیست که Atlas باید حتماً Termux libraries را مستقیماً embed کند.

تصمیم باید بر اساس:

```text
integration stability
license
version coupling
required surface
maintenance cost
```

گرفته شود.

---

# 15. Direct Library Coupling

سه گزینه معماری باید از هم جدا بررسی شوند:

```text
A) External Intent / IPC integration
B) Termux library integration
C) Fork / tightly coupled integration
```

فعلاً:

```text
C = not preferred
```

چون با اصل «Atlas جایگزین یا fork Termux نیست» در تضاد یا tension قرار می‌گیرد.

بین A و B باید برای هر capability evidence-driven تصمیم گرفت.

---

# 16. Quick Exec

Blueprintهای قدیمی از مفهومی مانند:

```text
Quick Exec
```

استفاده می‌کردند.

در مدل جدید، Quick Exec نباید Capability یا Action محسوب شود.

Mapping:

```text
Action
 ↓
Process Capability
 ↓
Execution Adapter
 ↓
Quick Exec / RUN_COMMAND / another mechanism
```

تا زمانی که prototype و source-level validation انجام نشده، Quick Exec فقط یک implementation candidate است.

---

# 17. Local Socket

Blueprintهای قدیمی همچنین:

```text
Socket / Local Socket
```

را برای ارتباط با Termux مطرح کرده‌اند.

منابع Termux فعلی نیز وجود library مربوط به local socket را در اکوسیستم Termux نشان می‌دهند؛ مستندات libraries به `lib-local-socket` و کلاس‌هایی مانند `Local*Socket` اشاره می‌کنند. citeturn1search5

اما وجود library به‌تنهایی به معنی مناسب‌بودن آن برای Atlas نیست.

قبل از adoption باید بررسی شود:

```text
Who owns the socket?
Authentication
Authorization
Lifecycle
Version compatibility
Failure recovery
Background behavior
Security
```

---

# 18. Termux:API

Termux:API نباید با Termux app execution API اشتباه گرفته شود.

مستندات رسمی Termux:API آن را به‌عنوان appی معرفی می‌کنند که Android APIها را برای command-line programs و scripts در دسترس قرار می‌دهد. citeturn1search4turn1search6

بنابراین:

```text
Termux:API
    ≠
Termux command execution bridge
```

Termux:API برای device capabilities مانند Android-level APIs مطرح است، نه به‌عنوان universal transport برای:

```text
interactive terminal
PTY
filesystem
sessions
```

این distinction باید حفظ شود.

---

# 19. Termux:API Architecture

منابع رسمی Termux:API توضیح می‌دهند که helper binary از local anonymous sockets برای انتقال stdin/stdout به API receiver استفاده می‌کند. citeturn1search4

این نکته مهم است:

> وجود socket در یک Termux subsystem به معنی آن نیست که همان socket mechanism باید transport اصلی Atlas باشد.

هر socket architecture باید در context خودش ارزیابی شود.

---

# 20. Filesystem Boundary

Filesystem یکی از حساس‌ترین integration boundaries است.

حداقل باید این فضاها جدا شوند:

```text
Termux private filesystem
Android shared/external storage
User-selected document/tree access
Atlas private storage
```

Atlas نباید اینها را یک filesystem واحد فرض کند.

---

# 21. Termux Private Filesystem

Commandهایی که در Termux context اجرا می‌شوند می‌توانند به محیط فایل Termux دسترسی داشته باشند.

RUN_COMMAND مستندات نیز working directory و executable path را در context Termux تعریف می‌کنند. citeturn0search1

برای Atlas، مسیرهایی مانند:

```text
$PREFIX
~
```

باید به‌عنوان Termux-specific paths در adapter شناخته شوند، نه به‌عنوان Android filesystem paths عمومی.

---

# 22. Shared / External Storage

برای external shared storage، Termux ممکن است به permission مناسب نیاز داشته باشد.

مستندات RUN_COMMAND تصریح می‌کنند که برای executable یا working directory در مسیرهای external shared storage، Termux باید storage access لازم را داشته باشد؛ نمونه‌های رایج شامل:

```text
~/storage
/sdcard
/storage/emulated/0
```

هستند. citeturn1search0turn1search13

بنابراین:

```text
Filesystem Capability
```

نباید فرض کند که هر path برای Termux قابل دسترسی است.

---

# 23. Android Storage vs Termux Storage

Atlas باید در سطح semantic بتواند distinction زیر را حفظ کند:

```text
Termux path
Android path
User-selected URI
Atlas-owned file
```

Resolution این pathها باید در integration boundary انجام شود.

Action نباید مجبور باشد از Android-specific URI mechanics خبر داشته باشد مگر اینکه خود Action ذاتاً Android document operation باشد.

---

# 24. FileProvider

Blueprintهای قدیمی FileProvider را به‌عنوان یکی از گزینه‌های integration مطرح کرده‌اند.

این سند FileProvider را نه approved و نه rejected اعلام می‌کند.

FileProvider فقط زمانی باید استفاده شود که:

```text
Atlas ↔ another Android app
```

نیاز به file sharing داشته باشد.

آن را نباید به‌عنوان جایگزین عمومی برای:

```text
Termux filesystem access
```

در نظر گرفت.

---

# 25. External App Integration

Integration با اپ‌های دیگر باید به سه دسته تقسیم شود:

```text
Termux integration
Android standard intents / URI grants
Termux plugin ecosystem
```

این سه mechanism نباید در یک abstraction مبهم ادغام شوند.

---

# 26. Package Visibility

Android target SDK 30+ می‌تواند package visibility را تحت تأثیر قرار دهد.

مستندات Termux برای RUN_COMMAND صراحتاً نیاز به visibility مناسب برای package `com.termux` را در این سناریو مطرح می‌کنند. citeturn1search0turn1search5

Atlas باید installation/integration diagnostics داشته باشد:

```text
Is Termux installed?
Can Atlas resolve Termux package?
Is expected service available?
Is package visible?
```

---

# 27. Background Execution

Android محدودیت‌های background execution و activity launch دارند.

مستندات Termux اشاره می‌کنند که برای terminal sessions که از background شروع می‌شوند، Android >=10 می‌تواند محدودیت ایجاد کند و در برخی شرایط Draw Over Apps permission مطرح می‌شود. citeturn1search0turn1search13

نتیجه:

```text
Atlas background request
        ↓
Termux integration
        ↓
Android background policy
        ↓
Possible delay / user intervention / failure
```

Atlas نباید این حالت را به‌صورت silent failure پنهان کند.

---

# 28. Battery Optimization

Termux documentation همچنین به aggressive process killing و battery optimization به‌عنوان یک عامل محیطی اشاره می‌کند. citeturn1search0

این مورد باید در Diagnostics به‌عنوان:

```text
Environment Risk
```

در نظر گرفته شود، نه به‌عنوان failure قطعی Termux.

---

# 29. Process Lifecycle

برای noninteractive execution:

```text
Action
 ↓
Start process
 ↓
Observe
 ↓
Exit
 ↓
Collect result
```

برای interactive:

```text
Action
 ↓
Create/attach session
 ↓
Process active
 ↓
stdin/stdout/stderr
 ↓
Resize / signals
 ↓
Exit / detach
```

این دو lifecycle باید جدا مدل شوند.

---

# 30. Signals

Action Architecture cancellation را تعریف کرده است.

Termux Integration باید مشخص کند cancellation چگونه به underlying process تبدیل می‌شود.

Candidate:

```text
Cancel
 ↓
Termux session/process control
 ↓
graceful signal
 ↓
force termination if necessary
```

این سند mechanism دقیق signal delivery را هنوز قفل نمی‌کند.

---

# 31. stdin / stdout / stderr

Atlas باید این سه مفهوم را از هم جدا نگه دارد:

```text
stdin
stdout
stderr
```

برای noninteractive:

```text
stdin → optional input
stdout → result/diagnostic stream
stderr → error/diagnostic stream
```

برای interactive:

```text
stdin/stdout/stderr
        ↕
Terminal session / PTY semantics
```

Raw streams باید توسط Adapter به semantic events/result تبدیل شوند.

---

# 32. Streaming

Intent-based execution برای control messages و resultهای کوچک مناسب است.

برای عملیات بسیار طولانی یا high-volume streaming، Atlas نباید فرض کند که Android Intent extras transport مناسبی است.

Candidate architecture:

```text
Control Plane
    → Intent / IPC

Data Plane
    → stream / file / session transport
```

این separation باید در prototype بررسی شود.

---

# 33. Execution Modes

نسخه فعلی Integration Model:

```text
Mode A
NonInteractive Command

Mode B
Background Process

Mode C
Interactive Terminal Session

Mode D
Long-running Session / Stateful Process
```

هر mode باید adapter semantics مستقل داشته باشد.

---

# 34. Mode Selection

Action نباید مستقیماً بگوید:

```text
Use RUN_COMMAND_BACKGROUND=true
```

بلکه:

```text
ExecutionMode = Background
```

را درخواست می‌کند.

Adapter implementation را انتخاب می‌کند.

این همان اصل:

> Command/transport is implementation. Action is abstraction.

---

# 35. Capability Discovery

Atlas باید بتواند قبل از اجرای Actionهای وابسته به Termux، وضعیت integration را بررسی کند.

مثلاً:

```text
Termux Installed
Termux Version
RUN_COMMAND Available
Permission State
External Apps Policy
Package Visibility
Storage Access
Interactive Session Support
```

اما discovery نباید تمام UI را به technical flags تبدیل کند.

Result باید semantic باشد:

```text
Ready
Unavailable
Needs Setup
Incompatible
Unknown
```

---

# 36. Integration Health

یک health model پیشنهادی:

```text
Healthy
Degraded
Needs User Setup
Unavailable
Incompatible
Unknown
```

مثلاً:

```text
Termux installed
+
RUN_COMMAND permission granted
+
allow-external-apps enabled
=
execution integration ready
```

ولی:

```text
Termux installed
+
external apps disabled
=
Needs User Setup
```

این taxonomy Candidate است.

---

# 37. Version Compatibility

Atlas نباید صرفاً بر اساس:

```text
Termux installed
```

تصمیم بگیرد.

باید capability/version compatibility را بررسی کند.

مثال:

```text
RUN_COMMAND basic execution
RUN_COMMAND result return
background execution
session features
result file features
```

ممکن است version requirements متفاوتی داشته باشند. مستندات رسمی برخی extras را به versionهای مشخص Termux وابسته می‌دانند. citeturn0search1

بنابراین:

```text
Termux Version
```

باید بخشی از integration diagnostics باشد.

---

# 38. Unsupported Mechanisms

Atlas باید بین این موارد distinction داشته باشد:

```text
Official / documented
Supported enough for prototype
Source-observed but not public contract
Undocumented / reverse-engineered
Unsupported
```

قانون:

> Source code evidence به معنی public API guarantee نیست.

اگر Atlas به source-observed mechanism وابسته شود، باید risk آن ثبت شود.

---

# 39. Official vs Internal API

مثلاً:

```text
RunCommandService
```

از نظر source code واقعی است و documentation رسمی برای RUN_COMMAND دارد.

اما direct dependency روی private internals یا internal classes نباید بدون دلیل architectural قفل شود.

ترجیح:

```text
Documented public integration boundary
```

سپس:

```text
Source-level integration
```

فقط وقتی لازم و justified باشد.

---

# 40. Adapter Responsibilities

`TermuxAdapter` مسئولیت‌های پیشنهادی:

```text
1. Detect Termux
2. Validate compatibility
3. Validate integration prerequisites
4. Translate ExecutionRequest
5. Select execution mechanism
6. Start execution
7. Receive result/events
8. Normalize failures
9. Track execution identity
10. Report lifecycle
11. Support cancellation where possible
12. Expose diagnostics
```

Adapter نباید:

```text
UI logic
Action semantics
Project business logic
```

را مالک شود.

---

# 41. Adapter Non-Responsibilities

Adapter نباید تصمیم بگیرد:

```text
"User wants to clone repository"
```

این Action/Intent concern است.

Adapter فقط باید بداند چگونه یک execution request معتبر را به Termux منتقل کند.

---

# 42. Execution Request

مدل مفهومی:

```text
ExecutionRequest
    mode
    executable
    arguments
    stdin?
    workingDirectory?
    environment?
    sessionPolicy?
    outputPolicy?
    timeoutPolicy?
    cancellationPolicy?
```

این object هنوز API نهایی نیست.

مهم این است که semantic Action Input از transport-specific extras جدا بماند.

---

# 43. Execution Result

مدل مفهومی:

```text
ExecutionResult
    executionId
    state
    exitCode?
    stdout?
    stderr?
    error?
    diagnostics
    artifacts?
```

سپس:

```text
ExecutionResult
        ↓
Action Result
```

Action result ممکن است semantic‌تر از execution result باشد.

---

# 44. Execution Identity

هر execution باید در صورت امکان یک identity داشته باشد:

```text
executionId
```

این برای:

```text
tracking
cancellation
result correlation
logging
unknown-state handling
```

مهم است.

RUN_COMMAND نیز execution/result context و execution id را در result path پشتیبانی می‌کند. citeturn1search1turn1search5

---

# 45. Unknown State at Integration Boundary

سناریو:

```text
Atlas sends execution
        ↓
Termux accepts
        ↓
Atlas loses response channel
```

Atlas نباید بگوید:

```text
Failed
```

فقط به دلیل قطع channel.

نتیجه ممکن است:

```text
Unknown
```

باشد.

این باید با Action Architecture هماهنگ بماند.

---

# 46. Retry at Integration Boundary

Retry فقط زمانی مجاز است که بتوانیم تشخیص دهیم execution اصلاً شروع نشده یا failure retryable بوده است.

مثلاً:

```text
Package visibility blocked
```

→ retry بی‌معنی.

اما:

```text
Transient transport failure before dispatch confirmation
```

→ ممکن است retry candidate باشد.

اگر execution ممکن است قبلاً شروع شده باشد:

```text
Retry
```

می‌تواند duplicate operation ایجاد کند.

---

# 47. Idempotency and Integration

Adapter باید از retry کور جلوگیری کند.

مثال:

```text
InstallPackage
```

ممکن است نسبتاً idempotent باشد.

اما:

```text
Run arbitrary script
```

لزومی ندارد.

بنابراین retry policy باید Action-aware باقی بماند.

---

# 48. Interactive Session Boundary

برای terminal session باید handle/identity قابل مشاهده وجود داشته باشد.

مدل:

```text
OpenTerminalSession
        ↓
SessionHandle
        ↓
Interactive IO
        ↓
SessionState
```

Session handle نباید raw Android object به Action layer باشد.

---

# 49. Session Lifecycle

Candidate:

```text
Requested
   ↓
Creating
   ↓
Active
   ↓
Detached
   ↓
Exited
```

failure paths:

```text
Creating → Failed
Active → Failed
Active → Unknown
```

جزئیات session persistence باید در آینده و بر اساس prototype تعیین شود.

---

# 50. Terminal Resize

Interactive terminal فقط stdin/stdout نیست.

Resize نیز جزء semantics terminal است:

```text
columns
rows
pixelWidth?
pixelHeight?
```

Atlas باید UI resize را از transport mechanism جدا نگه دارد.

---

# 51. Environment Variables

Execution request ممکن است environment context نیاز داشته باشد.

مثلاً:

```text
PATH
HOME
PWD
LANG
custom variables
```

Atlas نباید environment را بی‌دلیل overwrite کند.

اصل:

```text
Preserve Termux environment by default
+
explicit override when required
```

---

# 52. Working Directory

Working directory یک جزء مهم ExecutionRequest است.

Termux RUN_COMMAND آن را مستقیماً پشتیبانی می‌کند و default آن در documentation به Termux home اشاره شده است. citeturn0search1

Atlas باید distinction زیر را حفظ کند:

```text
Action Context
    project root

Execution Working Directory
    actual path passed to Termux
```

این دو یکی نیستند.

---

# 53. Shell vs Executable

Atlas باید ترجیح دهد executable و arguments را semantic/structured نگه دارد.

مدل مطلوب:

```text
executable = "/.../python"
arguments = ["-m", "venv", "..."]
```

به‌جای:

```text
shell = "bash -c '...'"
```

تا زمانی که shell semantics واقعاً لازم نیست.

این کار:

```text
quoting problems
injection risk
escaping complexity
```

را کاهش می‌دهد.

---

# 54. When Shell Is Necessary

برخی Actionها ذاتاً shell semantics دارند:

```text
RunShellScript
Pipelines
Redirection
Job Control
Complex Shell Workflow
```

در این موارد shell execution باید explicit باشد.

مثلاً:

```text
Action = RunScript
Execution = shell-backed
```

نه اینکه همه Actionها از shell command string استفاده کنند.

---

# 55. Command Injection Boundary

Atlas باید تا حد امکان از string concatenation برای command construction دوری کند.

بد:

```text
"git clone " + userInput
```

بهتر:

```text
executable
+
structured arguments
```

Shell escaping فقط وقتی لازم است که shell واقعاً بخشی از Action semantics باشد.

---

# 56. Filesystem Actions

Filesystem Actionها باید تا حد ممکن در semantic layer تعریف شوند:

```text
ReadFile
WriteFile
Copy
Move
Rename
Delete
CreateDirectory
ListDirectory
```

Adapter فقط mechanism دسترسی را تعیین می‌کند.

---

# 57. File Access Strategies

Candidate strategies:

```text
A. Execute file operations inside Termux context
B. Android-side filesystem APIs
C. Android Storage Access Framework
D. Shared URI/FileProvider handoff
```

هیچ‌کدام نباید universal فرض شوند.

انتخاب باید بر اساس:

```text
path type
ownership
permissions
operation
security
performance
```

انجام شود.

---

# 58. Strategic Rule for Files

Rule پیشنهادی:

```text
Termux-owned path
    → Termux-side mechanism candidate

Android user document
    → Android URI/SAF candidate

Cross-app file handoff
    → URI/FileProvider candidate

Atlas private file
    → Android-native storage candidate
```

این mapping باید با Project/Workspace model نهایی cross-check شود.

---

# 59. Termux:API Boundary

Termux:API را فقط برای capabilityهای Android-level مناسب در نظر بگیرید.

مثلاً:

```text
device sensors
battery
location
wifi-related APIs
notifications
```

در مقابل:

```text
Run command
PTY
interactive terminal
filesystem bridge
```

باید در Termux app integration layer ارزیابی شوند، نه با فرض Termux:API universalization.

Termux:API رسمی خود را appی برای expose کردن Android API به command-line programs معرفی می‌کند. citeturn1search4

---

# 60. Failure Normalization

Adapter باید failureهای Termux/Android را به دسته‌های Atlas map کند.

مثلاً:

```text
RUN_COMMAND permission missing
        ↓
Integration / Permission Error

Termux not installed
        ↓
CapabilityUnavailable

Executable missing
        ↓
ToolMissing

External storage denied
        ↓
Filesystem / Permission Error

Process exit != 0
        ↓
Process Failure
```

Mapping دقیق باید بعداً با Error taxonomy نهایی شود.

---

# 61. Diagnostics

هر integration failure باید در صورت امکان این context را داشته باشد:

```text
Termux version
Atlas integration version
execution mode
action id
execution id
relevant permissions
integration state
raw error
user-facing explanation
```

اما اطلاعات حساس نباید بی‌دلیل log شوند.

---

# 62. Privacy

RUN_COMMAND documentation هشدار می‌دهد که third-party caller در صورت دریافت result می‌تواند به session transcript یا stdout/stderr دسترسی پیدا کند. citeturn1search2

برای Atlas این یعنی:

```text
Result access
=
Data access
```

بنابراین output collection باید explicit و purpose-bound باشد.

Atlas نباید همه stdout/stderr را به‌صورت دائمی ذخیره کند.

---

# 63. Logging Policy

Logging باید سه سطح داشته باشد:

```text
User-visible
Diagnostic
Developer/debug
```

Default:

```text
minimum necessary
```

مخصوصاً برای:

```text
passwords
tokens
environment secrets
SSH credentials
private file content
```

باید redaction یا عدم ذخیره در نظر گرفته شود.

---

# 64. Security Model at Integration Boundary

مدل:

```text
Action Risk
    +
Termux Trust Boundary
    +
Android Permission Boundary
    +
Filesystem Boundary
    ↓
Execution Decision
```

وجود permission فنی نباید به معنی authorization معنایی برای هر Action باشد.

مثلاً:

```text
Atlas has RUN_COMMAND permission
```

به معنی:

```text
Every Action is automatically allowed
```

نیست.

---

# 65. Setup Experience

اگر integration آماده نیست، Atlas باید guided setup ارائه دهد.

مثلاً:

```text
Termux Integration
    ↓
Termux detected
    ↓
RUN_COMMAND permission missing
    ↓
Explain why
    ↓
Open appropriate settings
    ↓
Re-check
```

یا:

```text
allow-external-apps disabled
    ↓
Explain security implication
    ↓
Guide user
    ↓
Re-check
```

این setup باید reversible و transparent باشد.

---

# 66. No Silent Configuration Mutation

Atlas نباید بدون consent کاربر فایل‌های تنظیمات Termux را silently تغییر دهد.

خصوصاً:

```text
~/.termux/termux.properties
```

و تنظیم:

```text
allow-external-apps=true
```

باید به‌عنوان security-relevant configuration شناخته شود.

Atlas می‌تواند راهنمایی کند، اما تغییر خودکار باید تصمیم امنیتی جداگانه باشد.

---

# 67. Prototype Strategy

قبل از implementation بزرگ، سه prototype پیشنهاد می‌شود.

## Spike A — Noninteractive

```text
Atlas
 ↓
RUN_COMMAND
 ↓
simple command
 ↓
stdout/stderr/exit code
 ↓
Atlas result
```

هدف:

```text
IPC
permission
result transport
version behavior
```

## Spike B — Interactive

```text
Atlas
 ↓
interactive request
 ↓
Termux terminal/session
 ↓
stdin/stdout
 ↓
resize
 ↓
exit
```

هدف:

```text
session lifecycle
interactive transport
background restrictions
```

## Spike C — Filesystem

```text
Atlas
 ↓
Termux context
 ↓
private path
shared path
URI/shared file
```

هدف:

```text
path visibility
permissions
copy/move
large file transfer
```

---

# 68. Prototype Gate

هیچ‌کدام از این موارد قبل از Spike نباید به‌عنوان implementation truth در Blueprint نهایی نوشته شوند:

```text
PTY mechanism
Socket mechanism
Session API
FileProvider strategy
Direct library embedding
```

Prototype باید evidence تولید کند.

---

# 69. Candidate Architecture

نسخه فعلی معماری پیشنهادی:

```text
                         Atlas
                           │
                        Action
                           │
                    Execution Request
                           │
                    Termux Adapter
                           │
          ┌────────────────┼─────────────────┐
          ↓                ↓                 ↓
    Noninteractive     Interactive       Filesystem
          │                │                 │
     RUN_COMMAND      Session/PTY*       Path/URI*
          │                │                 │
          └────────────────┼─────────────────┘
                           ↓
                         Termux
                           ↓
                        Linux
```

`*` هنوز نیازمند validation/prototype است.

---

# 70. What Is Validated vs What Is Not

## Evidence-backed / documented

```text
RUN_COMMAND exists.
Third-party execution is supported.
RUN_COMMAND requires permission.
allow-external-apps is a required security check.
Package visibility can matter.
Working directory and arguments are supported.
stdin/result delivery are supported in documented scenarios.
Background/foreground runner concepts exist.
Termux has terminal/session machinery.
Termux publishes integration libraries.
Termux:API is a separate Android API exposure mechanism.
```

این موارد بر اساس منابع رسمی فعلی Termux بررسی شده‌اند. citeturn0search1turn0search0turn0search4turn1search4turn1search5

## Not yet validated for Atlas

```text
Best transport for all Action types
Best interactive session integration
Direct PTY ownership
Direct socket architecture
Best filesystem strategy
Best large-output streaming mechanism
Best direct library dependency
Exact cancellation mechanism
Exact signal strategy
Session persistence across process death
Optimal version support matrix
```

---

# 71. Decisions

تصمیمات این نسخه:

1. Termux backend/execution environment است.
2. Termux integration باید پشت Adapter boundary قرار گیرد.
3. RUN_COMMAND یک integration mechanism معتبر و مستند برای prototype است.
4. RUN_COMMAND نباید Capability سطح بالای Atlas محسوب شود.
5. Process Capability سطح abstraction بالاتری دارد.
6. Interactive terminal باید از noninteractive execution جدا مدل شود.
7. Termux:API universal execution API محسوب نمی‌شود.
8. Filesystem باید بر اساس path ownership و permission تفکیک شود.
9. Android Intent برای control/small result data مناسب است، نه payloadهای بزرگ به‌عنوان فرض عمومی.
10. Result باید در Adapter به semantic Atlas Result تبدیل شود.
11. Unknown state باید در قطع شدن channel حفظ شود.
12. Permission فنی به معنی authorization معنایی نیست.
13. Atlas نباید security-relevant Termux configuration را silently تغییر دهد.
14. Source code evidence به معنی public API guarantee نیست.
15. Direct fork/tight coupling با Termux فعلاً مسیر ترجیحی نیست.
16. Quick Exec، Socket و PTY implementation concerns هستند، نه Capability.
17. Prototype قبل از قفل‌کردن mechanismهای حساس الزامی است.

---

# 72. Open Questions

1. حداقل نسخه Termux قابل پشتیبانی Atlas چیست؟
2. آیا RUN_COMMAND برای تمام noninteractive Actionهای موردنیاز کافی است؟
3. آیا result transport برای workloadهای طولانی کافی است؟
4. بهترین interactive integration چیست؟
5. آیا Atlas باید از `terminal-view`/`terminal-emulator` استفاده کند؟
6. آیا direct library dependency ارزش coupling را دارد؟
7. PTY ownership دقیقاً کجاست؟
8. Session handle چگونه lifecycle می‌گیرد؟
9. Cancellation در interactive و background چگونه یکسان یا متفاوت می‌شود؟
10. Signal delivery چگونه expose می‌شود؟
11. Large output چگونه stream یا persist می‌شود؟
12. Filesystem private path چگونه resolve می‌شود؟
13. Shared storage چگونه مدیریت می‌شود؟
14. آیا SAF برای برخی Project roots لازم است؟
15. FileProvider دقیقاً در کدام cross-app use case لازم است؟
16. آیا Local Socket برای use case خاصی ضروری است؟
17. آیا background execution به foreground service در Atlas نیاز خواهد داشت؟
18. چه diagnostics برای device-specific restrictions لازم است؟
19. چه version matrix رسمی برای integration نگهداری شود؟
20. آیا Termux adapter باید چند backend mechanism داشته باشد و failover کند؟

این سؤال‌ها هنوز باز هستند.

---

# 73. Validation Gates

قبل از نهایی‌کردن Integration Contract:

### Execution

- [ ] RUN_COMMAND prototype موفق.
- [ ] Permission flow validated.
- [ ] External-app policy validated.
- [ ] Package visibility validated.
- [ ] stdout/stderr/result validated.
- [ ] Background execution validated.

### Interactive

- [ ] Session creation validated.
- [ ] stdin validated.
- [ ] stdout/stderr validated.
- [ ] resize validated.
- [ ] cancellation validated.
- [ ] process exit validated.
- [ ] background restrictions validated.

### Filesystem

- [ ] Termux private path access validated.
- [ ] shared storage access validated.
- [ ] permission behavior validated.
- [ ] large file behavior validated.
- [ ] URI/FileProvider strategy validated where required.

### Security

- [ ] RUN_COMMAND trust boundary documented.
- [ ] result privacy documented.
- [ ] secret redaction validated.
- [ ] no silent `allow-external-apps` mutation.

### Compatibility

- [ ] supported Termux versions tested.
- [ ] Android versions tested.
- [ ] package visibility tested.
- [ ] representative device/background behavior tested.

---

# 74. Architecture Verdict

```text
Termux Adapter Boundary
        ↓
        APPROVED AS DIRECTION

RUN_COMMAND
        ↓
        DOCUMENTED + PROTOTYPE CANDIDATE

Noninteractive execution
        ↓
        STRONG CANDIDATE

Interactive session mechanism
        ↓
        OPEN / NEEDS SPIKE

Direct PTY
        ↓
        NOT DECIDED

Local Socket
        ↓
        NOT DECIDED

FileProvider
        ↓
        CASE-SPECIFIC / NOT GENERAL TRANSPORT

Termux:API
        ↓
        SEPARATE ANDROID-API INTEGRATION

Filesystem strategy
        ↓
        NEEDS SPIKE

Final Termux Adapter API
        ↓
        DEFERRED
```

---

# 75. Final Reference Model

مدل فعلی Atlas بعد از سه سند معماری:

```text
                        ATLAS
                          │
                    Native Android UX
                          │
                       Intent
                          │
                       Action
                          │
                  Required Capabilities
                          │
                      Validation
                          │
                  Execution Request
                          │
                    Termux Adapter
                          │
          ┌───────────────┼────────────────┐
          ↓               ↓                ↓
   Noninteractive     Interactive      Filesystem
      execution         session         integration
          │               │                │
    RUN_COMMAND*       Session*        Path/URI*
          │               │                │
          └───────────────┼────────────────┘
                          ↓
                        Termux
                          ↓
                     Linux / Tools
                          ↓
                  Result / Events
                          ↓
                         Atlas
```

`*` mechanism-level candidates are subject to prototype validation.

اصل نهایی:

> **Atlas should depend on Termux capabilities and documented integration boundaries, not on accidental implementation details of Termux.**

و:

> **Use the simplest documented mechanism that satisfies the Action contract; introduce lower-level transport only when evidence proves it necessary.**

---

# Document Status

**Technical Architecture Draft / Validation Required**

این سند بر اساس:
- `01_Core_Philosophy.md`
- `archive/v01_11_Core_Philosophy_Audit.md`
- `02_Capability_Model.md`
- `03_Action_Architecture.md`
- و بررسی منابع رسمی فعلی Termux

تهیه شده است.

منابع خارجی اصلی:

- Termux `RUN_COMMAND` documentation
- Termux `RunCommandService` source
- Termux `TerminalSession` source
- Termux Libraries documentation
- Termux:API README

این سند عمداً بین **documented evidence**، **architecture proposal** و **open validation** تفاوت می‌گذارد.

**Next → `05_Project_Workspace.md`**

**End of Document**
