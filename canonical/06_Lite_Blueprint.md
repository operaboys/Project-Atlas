# Atlas — Lite Blueprint (Revised)

> سند: `06_Lite_Blueprint.md`
> Version: **2.0-architecture-aligned draft**
> Date: 2026-08-30
> Status: **Architecture Revision / Decision Candidate**
> Replaces: `archive/v01_01_Blueprint_Lite.md` (historical v1.0)
> Depends on: `01_Core_Philosophy.md`, `02_Capability_Model.md`, `03_Action_Architecture.md`, `04_Termux_Integration.md`, `05_Project_Workspace.md`
> Purpose: بازسازی کامل Blueprint Lite پس از تثبیت لایه‌های Core / Capability / Action / Termux / Context

---

# 1. Revision Intent

این سند patch روی Blueprint قدیمی Lite نیست.

Blueprint قدیمی محصول را عمدتاً به‌صورت مجموعه‌ای از صفحات و featureها تعریف می‌کرد:

```text
Home
Projects
Terminal
Files
Settings
```

و معماری را به شکل:

```text
UI
 ↓
Controller
 ↓
Command Bridge
 ↓
Termux
```

مدل جدید Atlas باید featureها را روی معماری تثبیت‌شده قرار دهد:

```text
UI
 ↓
Intent
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
UI
```

اصل:

> **Lite is a product scope, not a weaker architecture.**

Lite نباید نسخه‌ای از Atlas باشد که معماری آن حذف یا دور زده شده است.

---

# 2. Product Identity

Atlas Lite:

> **یک Native Android Graphical Shell / UX Layer برای اکوسیستم Termux/Linux است که عملیات رایج را به workflowهای بصری تبدیل می‌کند و Terminal را به‌عنوان مسیر کامل Power User حفظ می‌کند.**

Atlas Lite:

- Termux را replace نمی‌کند.
- Termux fork نیست.
- Linux distribution نیست.
- صرفاً terminal emulator نیست.
- صرفاً GUI wrapper چند command ثابت نیست.
- نباید محیط Linux را بی‌دلیل تغییر دهد.

مدل ذهنی:

```text
Atlas = UX + Intent + Action + Context
Termux/Linux = execution ecosystem
```

---

# 3. Core UX Principle

سه مسیر باید همزمان وجود داشته باشند:

```text
GUI
  ↓
برای کارهای قابل abstraction

Guided Workflow
  ↓
برای کارهای چندمرحله‌ای

Terminal
  ↓
برای قدرت کامل و مواردی که abstraction آن‌ها منطقی نیست
```

اصل:

> **GUI when possible. CLI when necessary.**

و:

> **Hide syntax by default. Never hide consequences.**

---

# 4. Architecture Reference

```text
┌──────────────────────────────┐
│       Native Android UI     │
│   Compose / Material 3      │
└──────────────┬───────────────┘
               ↓
        User Interaction
               ↓
          Intent Layer
               ↓
       Intent Resolution
               ↓
            Action
               ↓
   Required Capabilities
          + Context
               ↓
          Validation
               ↓
          Execution
               ↓
        Termux Adapter
               ↓
        Termux / Linux
               ↓
        Result / Events
               ↓
               UI
```

UI نباید مستقیماً command اجرا کند.

نام‌هایی مانند `Command Bridge` یا `Quick Exec` در صورت استفاده، implementation concern هستند؛ abstraction اصلی Atlas نیستند.

---

# 5. Capability Foundation

Lite روی این capabilityهای بنیادی بنا می‌شود:

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

اما حضور Capability به معنی فعال بودن تمام featureهای مربوط به آن در Lite نیست.

Feature admission بر اساس scope و validation انجام می‌شود.

Capabilityهای اصلی در مدل فعلی همچنان Candidate/Architecture-level هستند و implementation contract دقیق آن‌ها توسط اسناد تخصصی تعیین می‌شود.

---

# 6. Lite Scope Philosophy

Lite باید روی کارهایی تمرکز کند که:

1. برای کاربر Termux واقعاً تکرارشونده‌اند.
2. GUI یا Guided Workflow برایشان ارزش واضح دارد.
3. می‌توانند با Action model به‌صورت قابل اعتماد مدل شوند.
4. نتیجه و خطا قابل مشاهده باشد.
5. بدون ایجاد abstraction مصنوعی قابل پیاده‌سازی باشند.
6. Terminal escape hatch همیشه حفظ شود.

نباید صرفاً برای پر کردن یک صفحه، feature اضافه شود.

---

# 7. Navigation Model

Navigation پیشنهادی Lite:

```text
Home
Projects
Files
Terminal
Settings
```

این navigation یک UX decision است، نه معماری داخلی.

هر destination باید Action/Context architecture را مصرف کند.

---

# 8. Home

Home باید dashboard سبک و actionable باشد.

هدف:

> سریع‌ترین مسیر از ورود به Atlas تا کار واقعی.

محتوای پیشنهادی:

```text
Recent Projects
Recent / Suggested Actions
Quick Actions
System / Integration Status
Recent Activity
```

اطلاعاتی که ارزش واقعی برای تصمیم کاربر ندارند نباید صرفاً برای «زیباتر شدن dashboard» نمایش داده شوند.

---

# 9. Home — Recent Projects

Home می‌تواند حداکثر ۵ Project اخیر را نمایش دهد.

نمایش:

```text
Project name
Optional location summary
Last opened
Availability state
```

Tap:

```text
OpenProject
```

Long press / overflow:

```text
Project actions
```

Project باید طبق Project Model شناسایی شود، نه صرفاً با path string.

---

# 10. Home — Recent Actions

به‌جای مفهوم قدیمی «Popular Commands»، Lite بهتر است روی Actionها تمرکز کند.

مثلاً:

```text
Open File
Run Script
Install Package
Clone Repository
Open Terminal
```

در صورت نمایش command واقعی، command implementation نباید جای Action semantics را بگیرد.

---

# 11. Quick Actions

حداکثر ۴ quick action در Home.

Quick action باید:

```text
Action
+
known context
```

باشد.

مثلاً:

```text
Open Current Project
Open Terminal
New Project
Open File
```

Quick action نباید یک shell command hard-coded در UI باشد.

---

# 12. System Information

System information فقط وقتی نمایش داده شود که actionable یا contextually useful باشد.

موارد ممکن:

```text
Termux availability
Integration status
Storage availability
Battery
Memory
```

CPU/RAM/battery dashboard نباید الزام معماری باشد.

اگر telemetry هزینه performance یا privacy غیرضروری ایجاد کند، باید حذف یا محدود شود.

---

# 13. Integration Status

Home/Settings باید بتواند وضعیت integration را به زبان قابل فهم نمایش دهد.

مثلاً:

```text
Termux
✓ Available

Command execution
✓ Available

Interactive terminal
✓ Available / Needs validation

Filesystem access
Partial / Available / Needs permission
```

جزئیات implementation مانند Intent action names نباید در UX اصلی نمایش داده شوند مگر برای diagnostics.

---

# 14. Projects

Project screen برای مدیریت semantic Projectهاست.

هدف:

```text
Discover
Create
Open
Search
Organize
Forget
Reattach
```

Project صرفاً «یک پوشه» نیست.

---

# 15. Project List

هر Project می‌تواند:

```text
Display Name
Availability
Root summary
Last Opened
```

را نمایش دهد.

Stateهایی مانند:

```text
Available
Root Missing
Inaccessible
Archived
```

باید قابل تشخیص باشند.

---

# 16. Project Search

جستجو می‌تواند روی:

```text
Display Name
Known Root
Relevant metadata
```

انجام شود.

Search نباید به‌صورت پیش‌فرض کل filesystem را دائماً scan کند.

Discovery باید controlled و قابل مدیریت باشد.

---

# 17. Create Project

Workflow پایه:

```text
New Project
   ↓
Name
   ↓
Location
   ↓
Optional Type
   ↓
Validate
   ↓
Create / Initialize
   ↓
Open Project
```

Project Type می‌تواند:

```text
Unknown / Generic
Python
Node
Android
Web
...
```

باشد، اما Unknown باید معتبر باشد.

---

# 18. Open Project

Open Project:

```text
Resolve Project
 ↓
Validate Root
 ↓
Refresh Relevant Context
 ↓
Set Active Project
 ↓
Open Project UI
```

Open Project به معنی اجرای command خاص یا باز کردن یک screen خاص نیست.

---

# 19. Forget Project

`Forget Project`:

```text
Remove Atlas project metadata/reference
```

و:

```text
DO NOT delete filesystem content
```

این operation باید از deletion جدا باشد.

---

# 20. Reattach Project

اگر root از دست رفته باشد:

```text
Project
 ↓
Root Missing
 ↓
Locate New Root
 ↓
Validate
 ↓
Reattach
```

Atlas نباید صرفاً با نام directory identity را تأیید کند.

---

# 21. Delete Project Files

این operation destructive است.

مدل:

```text
Select Project
 ↓
Resolve current root
 ↓
Risk check
 ↓
Explicit confirmation
 ↓
Execute deletion
 ↓
Verify result
```

Cached root به‌تنهایی برای عملیات destructive کافی نیست.

---

# 22. Workspace in Lite

Workspace در مدل معماری تعریف شده است، اما Lite نباید آن را به یک IDE-style workspace manager تبدیل کند.

Current Lite stance:

```text
Single-project context = primary
Multi-project Workspace = supported by model, not mandatory UI complexity
```

اگر Workspace در Lite UI ارائه شود، باید lightweight باشد.

---

# 23. Files

Files در Lite باید روی resource operations رایج تمرکز کند.

مدل:

```text
Filesystem Capability
      ↓
File Actions
      ↓
File UI
```

نه:

```text
File UI
 ↓
direct shell command
```

---

# 24. Filesystem Domains

Atlas باید distinction بین resource domains را حفظ کند:

```text
Termux private filesystem
Android shared/external storage
URI / document resource
```

نباید همه چیز به یک `String path` تبدیل شود.

---

# 25. Home Directory

در صورت available بودن:

```text
Termux Home
```

می‌تواند primary filesystem location باشد.

کارهای رایج:

```text
Browse
Create Folder
Create File
Rename
Copy
Move
Delete
Open
```

باید از Action layer عبور کنند.

---

# 26. Android Storage

Android shared storage می‌تواند از طریق مکانیزم معتبر Android/URI access در اختیار Atlas قرار گیرد.

دسترسی نباید با assumption قدیمی «Atlas مستقیماً به Termux private path دسترسی دارد» طراحی شود.

Mechanism دقیق باید مطابق Integration Spec و validation واقعی باقی بماند.

---

# 27. File Actions

Actions پایه:

```text
OpenFile
CreateFile
CreateFolder
RenameFile
CopyFile
MoveFile
DeleteFile
```

هر Action باید:

```text
Input
Validation
Context
Result
Error
```

داشته باشد.

---

# 28. File Preview

Previewهای مناسب Lite:

```text
Images
Text
Markdown
Common metadata
```

Binary resources:

```text
metadata only
```

Syntax highlighting می‌تواند محدود و تدریجی باشد و نباید dependency سنگین بدون ارزش واقعی ایجاد کند.

---

# 29. File Safety

Destructive file operations:

```text
Delete
Overwrite
Move into protected/conflicting location
```

باید consequence را واضح کنند.

اصل:

> Hide syntax. Never hide consequences.

---

# 30. Terminal

Terminal همچنان قلب Atlas است.

Lite نباید Terminal را قربانی GUI abstraction کند.

Terminal باید تا حد ممکن:

```text
full-power
first-class
direct
```

باشد.

---

# 31. Terminal Sessions

Session model باید از Integration Spec پیروی کند.

دو مفهوم باید جدا بمانند:

```text
Noninteractive execution
```

و:

```text
Interactive session
```

اینها implementationاً و lifecycleاً یکسان فرض نمی‌شوند.

---

# 32. Terminal Tabs

عدد ثابت قدیمی «حداکثر ۱۰ تب» فعلاً decision معماری نیست.

Lite می‌تواند یک محدودیت عملی داشته باشد، اما مقدار نهایی باید بر اساس:

```text
device performance
memory
session mechanism
UX
```

benchmark شود.

---

# 33. Terminal Session State

Session state مفهومی:

```text
Active
Inactive
Closed
Unavailable
```

است.

Atlas نباید فرض کند که persistence metadata به معنی زنده ماندن process است.

---

# 34. Session Restoration

Restoration باید بین:

```text
Restore UI context
```

و:

```text
Restore live process/session
```

تفاوت بگذارد.

ممکن است Atlas بتواند:

```text
remember last terminal location
```

ولی نتواند یا نباید:

```text
restore exact live PTY
```

کند.

---

# 35. Terminal Input

Terminal باید input واقعی را به session mechanism بدهد.

در interactive mode:

```text
stdin
```

باید semantic جدا داشته باشد.

Input نباید به‌عنوان command string abstraction شود.

---

# 36. Terminal Output

Output باید تا حد امکان:

```text
stdout
stderr
ANSI/control semantics
```

را حفظ کند.

UI rendering می‌تواند presentation را تغییر دهد، ولی نباید محتوای command output را بی‌دلیل mutate کند.

---

# 37. Terminal Search

Search در:

```text
current output
history
```

می‌تواند ارائه شود.

Search یک UI capability است و نباید به backend command parser وابسته شود.

---

# 38. Copy / Paste

Lite باید:

```text
Select
Copy
Paste
```

را به شکل native Android ارائه کند.

Clipboard handling نباید command history را به‌طور ناخواسته expose کند.

---

# 39. Command History

History می‌تواند در Lite وجود داشته باشد.

اما:

```text
History = user data
```

و باید privacy-conscious باشد.

History نباید به‌صورت پیش‌فرض external telemetry شود.

---

# 40. Bookmarks

Bookmarked commands می‌توانند وجود داشته باشند، اما UX باید آنها را به‌عنوان:

```text
Saved Actions / Commands
```

تفکیک کند.

هر command bookmark در صورت اجرای مستقیم باید با consequence مناسب رفتار کند.

---

# 41. Quick Execution

Quick execution برای Actionهایی مناسب است که:

```text
noninteractive
bounded
observable
```

هستند.

نمونه:

```text
pwd
ls
df
script execution
package query
```

مکانیزم واقعی execution در Adapter تعیین می‌شود.

Action نباید مستقیماً `RUN_COMMAND` یا transport-specific flag را بشناسد.

---

# 42. Long-running Operations

عملیات طولانی باید observable باشند:

```text
Queued
Running
Progress
Completed
Partial Success
Warning
Failed
Cancelled
Timed Out
Unknown
```

UI باید در صورت وجود اطلاعات مناسب بتواند:

```text
Progress
Current Step
Output
Error
Cancel
```

را نمایش دهد.

---

# 43. Result Model

UI نباید فقط منتظر یک `String output` باشد.

نتیجه مفهومی:

```text
Result
 ├── status
 ├── output
 ├── diagnostics
 ├── error?
 ├── metadata
 └── timing?
```

schema دقیق در Action Architecture تعیین می‌شود.

---

# 44. Error UX

Error باید دو سطح داشته باشد:

```text
Human-readable explanation
+
Technical detail
```

نمونه categories:

```text
Permission
Tool Missing
Invalid Input
Network
Filesystem
Process
Termux Integration
Cancelled
Timeout
Unknown
```

پیام error نباید implementation detail را به کاربر عادی تحمیل کند.

---

# 45. Retry

Retry فقط وقتی مناسب است که semantic operation قابل تکرار باشد.

مثلاً:

```text
Network request
```

ممکن است retry داشته باشد.

اما:

```text
Delete
```

نباید کورکورانه retry شود.

---

# 46. Cancellation

Cancel باید بخشی از Action lifecycle باشد.

مدل:

```text
User Cancel
 ↓
Action cancellation
 ↓
Adapter cancellation
 ↓
Underlying process/session control
```

مکانیزم signal در Integration layer قرار دارد.

---

# 47. Settings

Settings باید configurationهای واقعاً user-facing را مدیریت کند.

دسته‌ها:

```text
Appearance
Terminal
Behavior
Privacy
Integration
Diagnostics
```

---

# 48. Appearance

Lite می‌تواند:

```text
Dark
Light
System / Material You
```

را ارائه کند.

OLED و Custom Theme می‌توانند بعد از validation UX/performance اضافه شوند.

اعداد و paletteهای Blueprint قدیمی نباید بدون نیاز immutable تلقی شوند.

---

# 49. Terminal Appearance

قابل تنظیم:

```text
Font family
Font size
Cursor style
Terminal theme
```

Font options نباید بدون بررسی licensing/packaging و APK size قفل شوند.

---

# 50. Terminal Font Size

محدوده قبلی:

```text
12sp–24sp
```

می‌تواند initial UX candidate باشد.

اما باید با accessibility و screen-size testing validate شود.

---

# 51. Shell Selection

Lite می‌تواند shell را به‌عنوان preference/context نشان دهد:

```text
Default shell
```

اما نباید فرض کند:

```text
bash
```

حتماً نصب یا shell پیش‌فرض Termux است.

Shell discovery باید runtime-based باشد.

---

# 52. Start Directory

ممکن است:

```text
Home
Last Session Directory
Project Root
Custom Location
```

به‌عنوان preference/context وجود داشته باشد.

اینها نباید با Project Root یکی فرض شوند.

---

# 53. Integration Settings

Settings باید امکان مشاهده وضعیت integration را بدهد:

```text
Termux availability
Permissions/access
Filesystem availability
Execution availability
Interactive support
```

Actionهای unavailable باید به جای crash، وضعیت قابل فهم ارائه کنند.

---

# 54. Privacy

Atlas Lite نباید:

- commandهای حساس را external ارسال کند.
- password/token/private key را log کند.
- shell configuration را بدون consent تغییر دهد.
- telemetry غیرضروری فعال کند.

---

# 55. Secrets

موارد زیر Project metadata یا history معمولی نیستند:

```text
Passwords
Private Keys
API Tokens
Secrets
```

Default:

```text
Do not persist
Do not log
Do not transmit
```

مگر اینکه یک secure architecture مستقل در آینده تعریف شود.

---

# 56. Logging

Default logging باید محدود باشد.

هرگز log نکن:

```text
password
private key
API token
sensitive terminal input
```

Diagnostic logging باید opt-in و controlled باشد.

---

# 57. Permissions

Permission باید:

```text
Just-in-time
Purpose explained
Minimal
Revocable
```

باشد.

Atlas نباید permissionهای آینده را از روز اول درخواست کند.

---

# 58. Termux Dependency

Lite باید Termux را به‌عنوان external execution ecosystem در نظر بگیرد.

اگر Termux unavailable باشد:

```text
Atlas can open
but execution-dependent Actions become unavailable
```

رفتار failure باید graceful باشد.

---

# 59. Termux Integration Boundary

Architecture boundary:

```text
Atlas Action
      ↓
Execution Adapter
      ↓
Termux integration
      ↓
Termux
```

Lite نباید implementationهای legacy مانند:

```text
Command Bridge
FileProvider
PTY
Socket
RUN_COMMAND
```

را به‌عنوان product-level contract معرفی کند.

هر کدام فقط در صورت validation و در لایه مناسب استفاده می‌شوند.

---

# 60. Execution Modes

Lite باید حداقل دو semantic mode را بشناسد:

```text
Noninteractive
Interactive
```

و در صورت نیاز:

```text
Background / Long-running
```

اما mode انتخابی باید از Action semantics بیاید، نه UI screen.

---

# 61. Network

Network Capability در Lite می‌تواند برای Actionهای ضروری استفاده شود.

مثلاً:

```text
Repository discovery
Package-related operations
Network-aware tools
```

اما Network feature به معنی ساخت Network Manager مستقل نیست.

---

# 62. Package

Package Capability می‌تواند در Lite فقط برای عملیات ضروری یا highly reusable exposed شود.

هر package workflow باید:

```text
tool availability
input
progress
result
error
```

را مدیریت کند.

Package Manager کامل می‌تواند scope Pro باشد.

---

# 63. Git

Blueprint تاریخی Git را Pro می‌دانست.

این تصمیم product-scope فعلی می‌تواند حفظ شود:

```text
Advanced Git UI → Pro / later
```

اما Capability Git در architecture وجود دارد.

این دو را نباید با هم اشتباه کرد:

```text
Capability exists
≠
Lite must expose full Feature
```

Lite می‌تواند Git-aware Project context را در آینده consume کند بدون داشتن Git Center کامل.

---

# 64. AI

AI در Lite:

```text
Deferred
```

است.

اگر AI در آینده اضافه شود:

```text
AI
 ↓
Intent
 ↓
Action Plan
 ↓
Atlas Actions
```

و هرگز:

```text
AI
 ↓
Unrestricted Shell
```

نخواهد بود.

---

# 65. Plugin System

Plugin Store / Plugin SDK در Lite:

```text
Out of scope
```

معماری باید فقط طوری باشد که بعداً extension layer بتواند روی Action/Capability contracts سوار شود.

---

# 66. Automation

Automation/Scheduler:

```text
Out of scope for Lite
```

اما Action model نباید automation را غیرممکن کند.

---

# 67. Cloud

Cloud sync:

```text
Out of scope
```

Project identity و metadata باید به شکلی طراحی شوند که future sync در صورت نیاز بتواند بررسی شود، بدون اینکه Lite به cloud وابسته شود.

---

# 68. Performance

اعداد تاریخی:

```text
Cold Start < 2 sec
RAM < 50 MB
APK < 15 MB
```

در این revision:

```text
Targets / hypotheses
```

هستند، نه architecture decisions.

قبل از قفل شدن:

```text
Benchmark
Device classes
Terminal rendering tests
Session tests
Filesystem tests
```

لازم است.

---

# 69. Android Version

`minSdk 24` از Blueprint تاریخی مستقیماً به implementation contract منتقل نمی‌شود.

minSdk باید پس از بررسی:

```text
Compose
Material 3
required Android APIs
Termux compatibility
target devices
```

تعیین شود.

---

# 70. Data / Persistence

Lite ممکن است برای:

```text
Project metadata
Settings
History
Bookmarks
Recent state
```

به persistence نیاز داشته باشد.

Technology انتخابی:

```text
Deferred
```

Room نباید صرفاً به دلیل حضور در Blueprint قدیمی به تصمیم قطعی تبدیل شود.

---

# 71. Persistence Boundary

Persist:

```text
semantic state
preferences
metadata
references
```

Do not persist directly:

```text
live process
PTY object
socket object
Android Activity
raw runtime handle
```

Runtime state باید reattach/reconstruct شود.

---

# 72. UI State vs Domain State

UI state:

```text
selected tab
expanded panel
scroll position
```

Domain state:

```text
Project
Action
Session metadata
Filesystem resource
```

باید جدا باشند.

UI نباید source of truth برای domain state باشد.

---

# 73. Architecture Red Flags

هر implementation جدید در Lite باید با این موارد بررسی شود:

```text
Button → execute("command")
```

```text
Feature → custom shell command format
```

```text
Screen → owns business logic
```

```text
Workspace → giant state container
```

```text
Project → path string only
```

```text
Delete → cached path without revalidation
```

```text
AI → unrestricted shell
```

```text
Termux transport → exposed to UI
```

---

# 74. Feature Admission Gate

هر Feature جدید باید از این gate عبور کند:

```text
Is it frequent?
      ↓
Does GUI/GUIDED provide real value?
      ↓
Can semantics be modeled as an Action?
      ↓
Are required Capabilities available?
      ↓
Can input be validated?
      ↓
Can result/error be represented?
      ↓
Can cancellation/recovery be handled where needed?
      ↓
Are consequences visible?
      ↓
Does it avoid unnecessary Core complexity?
```

اگر پاسخ منفی باشد:

```text
Defer / redesign / keep Terminal-first
```

---

# 75. Lite Feature Matrix

| Area | Lite | Architecture Status |
|---|---|---|
| Home | Yes | Core UX |
| Projects | Yes | Project Capability |
| Files | Yes | Filesystem Capability |
| Terminal | Yes | Terminal Capability |
| Settings | Yes | Core UX |
| Quick Actions | Yes | Action Layer |
| Recent Actions | Yes | Action/Context |
| Basic Package workflows | Candidate | Validate |
| Advanced Git UI | No / later | Pro candidate |
| SSH Manager | No / later | Pro candidate |
| AI | No | Deferred |
| Plugin Store | No | Deferred |
| Automation | No | Deferred |
| Cloud Sync | No | Deferred |
| Database Tools | No | Deferred |
| Docker | No | Deferred |

---

# 76. Historical Feature Reconciliation

Blueprint قدیمی Lite شامل موارد زیادی بود که اکنون باید با architecture جدید بازتفسیر شوند.

### Popular Commands

→ `Recent / Saved Actions`

### Command Bridge

→ `Execution Adapter / Integration concern`

### Quick Exec

→ `Noninteractive execution mechanism`

### PTY / Socket

→ `Interactive transport concern`

### Termux API

→ `Integration mechanism candidate`, not universal architecture

### Project folder

→ `Project entity + RootReference`

### Session persistence

→ `Context/UI restoration` جدا از live process restoration

### Room

→ `Persistence implementation candidate`

### Hilt

→ `DI implementation candidate`

---

# 77. What Was Deliberately Removed as a Decision

موارد زیر از status «قطعی» خارج شدند:

```text
Termux:API as universal integration
Room as mandatory database
Hilt as mandatory architecture decision
minSdk 24
Android 14 as fixed target
APK < 15MB
RAM < 50MB
Cold Start < 2 sec
10 terminal sessions
100 history entries
exact terminal font list
exact theme palette
exact filesystem permission model
```

این به معنی ممنوع بودن آنها نیست.

به معنی:

> **Evidence first, then lock.**

---

# 78. What Remains Product-Important

این موارد هنوز ستون‌های Lite هستند:

```text
Native Android UX
Graphical workflows
First-class Terminal
Projects
Files
Action-oriented architecture
Termux/Linux execution
Context awareness
Observable operations
Clear errors
Safe destructive operations
Lightweight experience
```

---

# 79. Success Definition

معیار کیفی اصلی حفظ می‌شود:

> کاربر Atlas را باز کند و احساس کند با یک ابزار Android مدرن کار می‌کند، نه اینکه صرفاً یک پوسته متفاوت روی Termux باز کرده باشد.

اما موفقیت نباید فقط با زیبایی UI سنجیده شود.

Lite باید نشان دهد:

```text
Less syntax
Less friction
Same underlying power
Visible consequences
Reliable execution
```

---

# 80. Validation Metrics

Metrics تاریخی مانند:

```text
90% terminal open in first 5 min
80% satisfaction
70% recommendation
4.5+ stars
Crash < 1%
```

فعلاً target hypotheses هستند.

Metrics واقعی باید پس از prototype و user testing تعیین شوند.

---

# 81. Required Validation Before Implementation Lock

قبل از implementation contract نهایی:

```text
[ ] Termux installation/availability behavior
[ ] Noninteractive execution
[ ] Interactive session
[ ] stdin/stdout/stderr
[ ] cancellation
[ ] filesystem access
[ ] Android storage/URI access
[ ] Project discovery
[ ] Project identity persistence
[ ] Reattach
[ ] Session restoration semantics
[ ] destructive file safety
[ ] Action result/error UX
[ ] terminal rendering performance
[ ] memory behavior
[ ] minSdk decision
```

---

# 82. Recommended Build Order

بعد از این Blueprint:

```text
Architecture
    ↓
Integration / feasibility validation
    ↓
Core domain models
    ↓
Action engine
    ↓
Termux adapter
    ↓
Project / filesystem context
    ↓
Terminal
    ↓
Projects
    ↓
Files
    ↓
Home
    ↓
Settings
    ↓
Hardening / testing
```

UI polish نباید قبل از تثبیت execution path تبدیل به blocker شود.

---

# 83. Lite Does Not Mean Toy

Lite باید:

```text
smaller scope
```

داشته باشد، نه:

```text
simplistic architecture
```

حتی Lite باید از ابتدا:

```text
Intent
Action
Capability
Context
Result
Error
Adapter
```

را به‌درستی جدا کند.

---

# 84. Lite / Pro Boundary

مرز Lite و Pro باید بر اساس:

```text
scope
complexity
advanced workflows
integration breadth
```

تعریف شود.

نه بر اساس:

```text
Lite = bad architecture
Pro = good architecture
```

هر دو باید یک architecture core مشترک داشته باشند.

---

# 85. Final Reference Model

```text
                         ATLAS LITE
                              │
                     Native Android UX
                              │
                         User Intent
                              │
                       Intent Resolution
                              │
                            Action
                              │
                    Capabilities + Context
                              │
                         Validation
                              │
                          Execution
                              │
                       Termux Adapter
                              │
                        Termux / Linux
                              │
                 ┌────────────┼────────────┐
                 ↓            ↓            ↓
             Filesystem     Process     Terminal
                 │            │            │
                 └────────────┼────────────┘
                              ↓
                        Result / Events
                              ↓
                             UI
```

---

# 86. Final Architectural Verdict

Blueprint Lite تاریخی برای شروع product thinking مفید بود، اما برای implementation مستقیم بیش از حد feature-driven و در چند نقطه وابسته به assumptions فنی اثبات‌نشده بود.

این Revision آن را به architecture تثبیت‌شده Atlas متصل می‌کند.

تصمیم اصلی:

> **Lite یک subset از capabilities/actions exposed to users است، نه یک معماری جداگانه.**

و:

> **Termux mechanism باید پشت Adapter بماند.**

و:

> **Project، Workspace، Filesystem، Session و Environment باید context/resource semantics خود را حفظ کنند و در یک abstraction مبهم ادغام نشوند.**

---

# 87. Document Status

**Architecture Revision / Decision Candidate**

این سند جایگزین سند بایگانی‌شده `archive/v01_01_Blueprint_Lite.md` برای مراحل بعدی معماری است.

مواردی که با برچسب:

```text
Candidate
Deferred
Needs Validation
```

مشخص شده‌اند نباید بدون evidence به implementation contract تبدیل شوند.

مرحله بعد:

```text
15 Revised Lite Blueprint
        ↓
UI / Interaction / Component Blueprint
        ↓
Validation / Prototype Gates
        ↓
Roadmap
        ↓
Pro Reassessment
```

**End of Document**
