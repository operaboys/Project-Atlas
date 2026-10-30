# Atlas — Project & Workspace Model

> سند: `05_Project_Workspace.md`
> Version: `1.0-draft`
> Date: 2026-08-30
> Status: **Architecture Draft / Decision Candidate**
> Position: **05 — Project & Workspace**
> Depends on: `01_Core_Philosophy.md`, `02_Capability_Model.md`, `03_Action_Architecture.md`, `04_Termux_Integration.md`
> Next: `06_Lite_Blueprint.md`

---

# 1. Purpose

این سند مدل معنایی Project و Workspace در Atlas را مشخص می‌کند.

سؤال اصلی:

> Project و Workspace در Atlas دقیقاً چه هستند، چه رابطه‌ای با filesystem و Termux دارند، چه چیزی را مالک می‌شوند، و چه چیزی را نباید مالک شوند؟

این سند باید ابهام‌های قبلی را حل کند، بدون اینکه Core را با implementation جزئیات غیرضروری قفل کند.

اصل پایه:

```text
Project ≠ Directory
Workspace ≠ Project
Workspace ≠ UI Screen
```

در عین حال:

```text
Project ↔ Filesystem
Project ↔ Environment
Project ↔ Repository
Project ↔ Actions
Workspace ↔ active context
```

روابط دقیق در ادامه تعریف می‌شوند.

---

# 2. Architectural Position

مدل فعلی Atlas:

```text
User
  ↓
Intent
  ↓
Action
  ↓
Context
  ├── Workspace
  ├── Project
  ├── Location
  ├── File
  ├── Repository
  ├── Environment
  └── Session
```

Project و Workspace باید بخشی از Context Model باشند، نه جایگزین آن.

---

# 3. Core Principle

Project یک entity معنایی است که یک محدوده کاری قابل تشخیص را برای Atlas تعریف می‌کند.

Project می‌تواند به یک filesystem root متصل باشد، اما هویت آن نباید صرفاً از path استخراج شود.

مدل پیشنهادی:

```text
Project
 ├── Identity
 ├── Root
 ├── Metadata
 ├── Tooling Context
 ├── Repository Context?
 ├── Environment Context?
 └── Persistence
```

برخی اجزا optional هستند.

---

# 4. Why Project Is Not Just a Directory

یک directory فقط یک filesystem location است.

Project علاوه بر location می‌تواند:

```text
name
identity
tooling assumptions
environment
repository context
preferred actions
recent state
```

داشته باشد.

مثلاً دو project می‌توانند در دو path متفاوت قرار داشته باشند ولی structure و tooling مشابه داشته باشند.

یا یک directory می‌تواند:

```text
directory
```

باشد بدون اینکه Atlas آن را Project بداند.

---

# 5. Project Identity

Project باید identity پایدار داشته باشد.

مدل مفهومی:

```text
ProjectId
    stable identity
```

این identity نباید برابر path باشد.

چرا؟

چون:

```text
Project A
    /data/project-a

move

Project A
    /data/archive/project-a
```

می‌تواند همچنان همان Project باشد.

در نتیجه:

```text
ProjectId ≠ RootPath
```

این یک تصمیم معماری مهم است.

---

# 6. Project Root

Project می‌تواند یک root داشته باشد:

```text
Project
    root = /path/to/project
```

Root، محدوده filesystem اصلی Project را مشخص می‌کند.

اما:

```text
root ≠ identity
```

Root ممکن است تغییر کند، در حالی که identity حفظ شود، مشروط به اینکه Atlas بتواند continuity را با اطمینان تشخیص دهد.

---

# 7. Root Validity

Root باید وضعیت داشته باشد.

Candidate:

```text
Valid
Missing
Inaccessible
Moved
Unknown
```

مثال:

```text
Project exists
+
root missing
```

نباید نتیجه شود:

```text
Project deleted
```

ممکن است root moved یا temporarily unavailable باشد.

---

# 8. Project Metadata

Metadata باید حداقلی و purposeful باشد.

Candidate:

```text
ProjectId
DisplayName
RootReference
CreatedAt
LastOpenedAt
ProjectType?
RepositoryReference?
EnvironmentReference?
```

این schema نهایی نیست.

اصل:

> Metadata نباید فقط برای پرکردن database ساخته شود.

---

# 9. Project Name

`DisplayName` و filesystem directory name باید جدا باشند.

مثلاً:

```text
DisplayName:
Atlas Development

Root:
~/projects/atlas
```

این اجازه می‌دهد نام UI مستقل از path باشد.

---

# 10. Project Type

Project Type می‌تواند در آینده برای discovery و UX مفید باشد:

```text
Generic
Android
Python
Node
Rust
Go
Web
Mixed
Unknown
```

اما Project Type نباید به‌عنوان شرط ایجاد Project اجباری شود.

Unknown باید معتبر باشد.

---

# 11. Project Detection

Atlas ممکن است Project را از مواردی مانند:

```text
directory structure
.git
build files
package manifests
Gradle files
pyproject
package.json
Cargo.toml
```

تشخیص دهد.

اما:

```text
Detected ≠ Confirmed
```

Auto-detection باید پیشنهاد باشد، نه الزام.

---

# 12. User-Created vs Detected Project

دو مسیر:

```text
User-created Project
```

و:

```text
Detected Project
```

Detected Project می‌تواند تا زمان تأیید user یک candidate باشد.

این distinction از ایجاد projectهای جعلی جلوگیری می‌کند.

---

# 13. Project Lifecycle

Lifecycle پیشنهادی:

```text
Discovered
   ↓
Candidate
   ↓
Confirmed
   ↓
Active
   ↓
Inactive
   ↓
Archived
```

حالت‌های failure:

```text
Confirmed → RootMissing
Confirmed → Inaccessible
Active → Unavailable
```

Archive به معنی حذف filesystem نیست.

---

# 14. Project Deletion

حذف Project از Atlas نباید به‌طور پیش‌فرض به معنی حذف root filesystem باشد.

دو operation باید جدا باشند:

```text
Forget Project
```

و:

```text
Delete Project Files
```

این distinction برای safety حیاتی است.

---

# 15. Forget Project

`Forget Project` یعنی:

```text
Remove Atlas project metadata/reference
```

بدون:

```text
Delete filesystem content
```

این operation باید low-riskتر از filesystem deletion باشد.

---

# 16. Delete Project Files

این operation destructive است:

```text
Project
 ↓
Delete root contents
```

باید از Project metadata operation جدا بماند و Action/Permission/Risk model را طی کند.

---

# 17. Project ↔ Repository

Repository بخشی از Project identity نیست.

یک Project ممکن است:

```text
Git repository
```

داشته باشد، اما Git repository الزاماً Project نیست.

مدل:

```text
Project
   └── Repository Context?
```

نه:

```text
Project = Git repository
```

---

# 18. Repository Context

Repository Context می‌تواند شامل:

```text
provider?
remote?
branch?
HEAD?
dirty state?
worktree?
```

باشد.

این context dynamic است و نباید همه آن به‌صورت static Project metadata ذخیره شود.

---

# 19. Git State

Git state باید هنگام نیاز refresh شود.

مثلاً:

```text
Project opened
   ↓
Git state queried
   ↓
Repository Context updated
```

نباید Atlas فرض کند state ذخیره‌شده همیشه truth است.

---

# 20. Project ↔ Environment

Project ممکن است environment context داشته باشد:

```text
runtime
toolchain
package manager
virtual environment
environment variables
```

اما environment باید entity/context مستقل باقی بماند.

مدل:

```text
Project
   ↓
Environment Context
```

نه:

```text
Project owns all environment state
```

---

# 21. Environment Identity

Environment می‌تواند:

```text
System
Termux
Virtual Environment
Project-local Environment
Custom
```

باشد.

Implementation دقیق environment management هنوز deferred است.

---

# 22. Project ↔ Termux

Project root می‌تواند داخل Termux filesystem باشد یا در shared Android storage.

بنابراین:

```text
Project
   ↓
Root Reference
   ↓
Filesystem Domain
```

و:

```text
Filesystem Domain
    Termux
    Android Shared
    URI/SAF
    Other
```

نباید root را صرفاً به Termux path تبدیل کنیم.

---

# 23. Root Reference

مدل مفهومی:

```text
RootReference
    kind
    location
    access mode
```

مثلاً:

```text
TermuxPath
AndroidPath
DocumentUri
```

schema نهایی deferred است.

---

# 24. Path vs URI

Atlas باید distinction زیر را حفظ کند:

```text
Path
```

و:

```text
URI
```

اینها دو representation متفاوت برای resource location هستند.

Action layer نباید بی‌دلیل همه چیز را به string path تبدیل کند.

---

# 25. Workspace Definition

Workspace یک context فعال برای کاربر است.

تعریف پیشنهادی:

> Workspace مجموعه‌ای از Projectها، locations، sessions و stateهای مرتبط است که در یک بازه کاری فعال Atlas به‌عنوان context جاری در نظر گرفته می‌شود.

Workspace الزاماً filesystem directory نیست.

---

# 26. Workspace ≠ Directory

یک Workspace می‌تواند شامل:

```text
Project A
Project B
Terminal Session
File
Repository
Environment
```

باشد.

پس:

```text
Workspace
    ≠
single root directory
```

این همان جایی است که Workspace از Project جدا می‌شود.

---

# 27. Workspace Scope

Workspace می‌تواند کوچک باشد:

```text
1 Project
```

یا گسترده‌تر:

```text
Project A
Project B
shared terminal
related repository
```

اما چند-project workspace نباید default complexity UI را افزایش دهد.

---

# 28. Workspace Identity

Workspace نیز در صورت persistence باید identity داشته باشد:

```text
WorkspaceId
```

اما برخلاف Project، Workspace بیشتر یک grouping/context است تا resource ownership entity.

---

# 29. Workspace Membership

یک Project می‌تواند در چند Workspace قرار بگیرد.

مدل:

```text
Project A
   ├── Workspace X
   └── Workspace Y
```

این به معنی duplicate شدن Project نیست.

Workspace رابطه/context است.

---

# 30. Workspace Ownership

Workspace نباید مالک filesystem resourceها شود.

مدل:

```text
Project owns/represents project semantics
Workspace groups/activates context
```

Workspace حذف شود:

```text
Projects remain
Files remain
Repositories remain
```

مگر اینکه user صریحاً operation دیگری انجام دهد.

---

# 31. Active Workspace

در هر لحظه Atlas می‌تواند یک active workspace داشته باشد:

```text
ActiveWorkspace?
```

ممکن است هیچ Workspace صریحی active نباشد.

در آن حالت Atlas می‌تواند:

```text
No Workspace
```

را معتبر بداند.

---

# 32. Active Project

در Workspace ممکن است یک Project فعال‌تر از بقیه باشد:

```text
ActiveWorkspace
    ↓
ActiveProject
```

اما:

```text
ActiveProject
```

نباید جای Workspace را بگیرد.

---

# 33. Active Context

مدل کامل‌تر:

```text
Active Workspace
    ↓
Active Project
    ↓
Current Location
    ↓
Current File
    ↓
Current Repository
    ↓
Current Session
```

هر سطح optional است.

---

# 34. Context Resolution

Action می‌تواند explicit context دریافت کند:

```text
RenameFile(file = X)
```

یا context جاری را consume کند:

```text
RenameCurrentFile
```

اما implicit context باید قابل trace باشد.

مثلاً UI باید بتواند مشخص کند:

```text
Current project: Atlas
Current file: MainActivity.kt
```

---

# 35. Context Ambiguity

اگر:

```text
Workspace has 3 projects
```

و Action می‌گوید:

```text
Build Project
```

بدون target مشخص، ambiguity وجود دارد.

Atlas نباید حدس بزند.

مدل:

```text
Ambiguous Context
    ↓
Ask / Select
    ↓
Resolved Action
```

---

# 36. Workspace Persistence

Persistence باید state را حفظ کند، نه همه runtime objectها را.

مناسب برای persistence:

```text
WorkspaceId
Project memberships
Last active project
Open locations
User preferences
Recent sessions metadata
```

نامناسب برای persistence مستقیم:

```text
live process object
PTY object
Android Activity reference
raw socket object
```

Runtime state باید دوباره attach/reconstruct شود.

---

# 37. Project Persistence

حداقل persistence می‌تواند:

```text
ProjectId
DisplayName
RootReference
CreatedAt
LastOpenedAt
metadata
```

باشد.

Dynamic state باید هنگام نیاز refresh شود.

---

# 38. Recent State

Recent state یک convenience layer است، نه source of truth.

مثلاً:

```text
Last opened file
Last terminal directory
Last selected project
Last used action
```

ممکن است تغییر کرده یا دیگر وجود نداشته باشد.

پس:

```text
Recent State
    ≠
Current Truth
```

---

# 39. Session Association

Terminal session می‌تواند با Project مرتبط باشد:

```text
TerminalSession
    projectId?
```

اما session نباید Project را مالک شود.

اگر Project حذف/forget شود:

```text
Session
```

ممکن است هنوز runtime active باشد.

این حالت باید explicit handling داشته باشد.

---

# 40. Environment Association

همین اصل برای Environment:

```text
Environment
    projectId?
```

یا:

```text
Project
    environmentReference?
```

اما lifecycle environment مستقل باقی می‌ماند.

---

# 41. Location Context

Location می‌تواند:

```text
Project root
subdirectory
file
repository root
terminal working directory
```

باشد.

Location باید semantic باشد و در صورت نیاز با filesystem reference resolve شود.

---

# 42. Current Directory

Terminal current working directory با Project root یکی نیست.

مثلاً:

```text
Project Root:
~/projects/atlas

Terminal CWD:
~/projects/atlas/scripts
```

این distinction باید حفظ شود.

---

# 43. Workspace and Terminal

Workspace می‌تواند چند Terminal Session مرتبط داشته باشد:

```text
Workspace
   ├── Project A
   ├── Project B
   ├── Terminal Session 1
   └── Terminal Session 2
```

این ارتباط contextual است.

---

# 44. Workspace and Files

Workspace می‌تواند recent/open files را track کند، اما فایل ownership را برعهده نمی‌گیرد.

```text
File
  belongs to filesystem/resource domain

Workspace
  references current/recent context
```

---

# 45. Project and Files

Project root یک boundary منطقی برای file/resource discovery است.

اما Project نباید ادعا کند که همه resourceهای زیر root همیشه متعلق به آن هستند.

Symlink، nested repository و mounted paths می‌توانند exception ایجاد کنند.

---

# 46. Nested Projects

Nested projectها ممکن‌اند:

```text
Project A
└── Project B
```

اما باید از ambiguity جلوگیری شود.

Atlas نباید صرفاً با دیدن `.git` یا manifest، تعداد زیادی Project خودکار ایجاد کند.

Detection باید conservative باشد.

---

# 47. Monorepo

یک monorepo می‌تواند:

```text
1 repository
multiple packages
multiple logical projects
```

داشته باشد.

بنابراین:

```text
Repository ≠ Project
```

و:

```text
Directory ≠ Project
```

این distinction برای future Git/Build UX مهم است.

---

# 48. Multi-root Project

Project ممکن است در آینده چند root داشته باشد.

مثلاً:

```text
Project
 ├── frontend root
 └── backend root
```

اما این قابلیت در نسخه فعلی implementation requirement نیست.

Current default:

```text
one primary root
```

Multi-root باید در آینده با use case واقعی justified شود.

---

# 49. Remote Project

Remote project نیز ممکن است در آینده وجود داشته باشد:

```text
SSH
Remote development
Cloud
```

اما در این سند remote project implementation تعریف نمی‌شود.

مدل فعلی باید طوری باشد که Root Reference را بیش از حد به local filesystem محدود نکند، بدون اینکه remote complexity را اکنون وارد Core کند.

---

# 50. Project Templates

Template و Project یکی نیستند.

```text
Template
   → creates/initializes Project
```

Template metadata نباید جزو Project identity شود.

---

# 51. Project Type Detection and Tooling

Tooling discovery می‌تواند:

```text
Project Type
Toolchain
Package Manager
Git
```

تشخیص دهد.

اما discovery result باید قابل refresh باشد.

مثلاً:

```text
Toolchain changed
```

بعد از project creation ممکن است environment context تغییر کند.

---

# 52. Project State

State پیشنهادی:

```text
Known
Available
Active
Unavailable
Archived
```

جزئیات نهایی state machine هنوز می‌تواند تغییر کند.

اصل مهم:

```text
metadata existence
≠
filesystem availability
```

---

# 53. Workspace State

Workspace state پیشنهادی:

```text
Persisted
Active
Inactive
Unavailable Context
```

Workspace نباید به خاطر unavailable شدن یک Project به‌طور خودکار حذف شود.

---

# 54. Missing Root Recovery

اگر root missing شد:

```text
Project
 ↓
Root Missing
 ↓
Locate / Reattach
```

کاربر باید بتواند در صورت امکان root جدید را معرفی کند.

اگر identity Project حفظ شود:

```text
ProjectId
```

می‌تواند باقی بماند.

---

# 55. Reattach

Reattach operation:

```text
Existing Project
        ↓
New Root
        ↓
Validation
        ↓
Reattached
```

Validation باید بررسی کند که root جدید واقعاً با Project موردنظر سازگار است.

نباید فقط به نام directory اعتماد شود.

---

# 56. Project Discovery After Reinstall

در صورت reinstall یا پاک‌شدن Atlas metadata، filesystem ممکن است هنوز باقی باشد.

Atlas باید بتواند:

```text
Discover
Candidate Project
Reconfirm
```

را در آینده پشتیبانی کند.

این دلیل دیگری است که:

```text
ProjectId
```

و:

```text
RootPath
```

نباید یکی باشند.

---

# 57. Persistence Technology

این سند database انتخاب نمی‌کند.

گزینه‌های implementation در آینده:

```text
DataStore
SQLite
Room
File-based metadata
```

اما Project/Workspace model باید مستقل از persistence technology تعریف شود.

---

# 58. Persistence Boundary

Business model:

```text
Project
Workspace
```

باید از storage model جدا باشد.

مدل نامطلوب:

```text
Room Entity = Domain Model
```

به‌عنوان تصمیم معماری فعلی توصیه نمی‌شود.

---

# 59. Serialization

اگر Project/Workspace persistence به serialization نیاز داشته باشد، schema باید versionable باشد.

مثلاً:

```text
schemaVersion
```

اما format نهایی deferred است.

---

# 60. Migration

Project/Workspace data در آینده ممکن است تغییر کند.

بنابراین migration باید در design لحاظ شود.

اما migration engine فعلاً خارج از scope این سند است.

---

# 61. Project Actions

Actionهای احتمالی:

```text
CreateProject
OpenProject
ForgetProject
ReattachProject
ArchiveProject
RenameProject
ChangeProjectRoot
```

اینها Actionهای semantic هستند.

Implementation نباید مستقیماً به Project screen متصل شود.

---

# 62. Workspace Actions

Actionهای احتمالی:

```text
CreateWorkspace
OpenWorkspace
CloseWorkspace
AddProjectToWorkspace
RemoveProjectFromWorkspace
SwitchWorkspace
```

اما Workspace UI/UX بعداً طراحی خواهد شد.

---

# 63. Open Project Semantics

`OpenProject` نباید الزاماً به معنی:

```text
Launch one screen
```

باشد.

می‌تواند:

```text
Resolve project
Load context
Validate root
Refresh relevant state
Set active context
```

باشد.

---

# 64. Close Project Semantics

`CloseProject` باید از:

```text
ForgetProject
DeleteProject
```

جدا باشد.

Close فقط context active را تغییر می‌دهد.

---

# 65. Open Workspace Semantics

Opening Workspace می‌تواند:

```text
load membership
restore recent context
restore project selection
reattach available sessions
```

را attempt کند.

اگر یک resource در دسترس نباشد، کل Workspace الزاماً fail نمی‌شود.

---

# 66. Partial Workspace Recovery

مثلاً:

```text
Workspace
 ├── Project A ✓
 ├── Project B ✗ root missing
 └── Session C ✓
```

نتیجه:

```text
Workspace = Partially Available
```

نه:

```text
Workspace = Failed
```

این با Partial Success و Unknown semantics در Action Architecture هم‌راستا است.

---

# 67. Workspace Complexity Guard

Workspace نباید به بهانه flexibility تبدیل شود به:

```text
IDE workspace manager
+
project graph
+
dependency graph
+
cloud workspace
+
remote workspace
```

در نسخه فعلی فقط semantic grouping/context لازم است.

---

# 68. Project Complexity Guard

Project نیز نباید تبدیل شود به:

```text
everything about a developer
```

Project باید فقط context لازم برای:

```text
actions
resources
tooling
state
```

را نگه دارد.

---

# 69. Ownership Model

مدل پیشنهادی:

```text
Atlas
 │
 ├── Project identity / metadata
 │
 ├── Workspace context
 │
 └── References to external resources
          │
          ├── Filesystem
          ├── Git repository
          ├── Termux environment
          └── Terminal sessions
```

Atlas لزوماً مالک همه این resourceها نیست.

---

# 70. External Resource Principle

هر resource خارجی باید lifecycle خودش را داشته باشد.

مثلاً:

```text
Git repository
```

ممکن است مستقل از Atlas باقی بماند.

همین‌طور:

```text
Termux environment
```

و:

```text
filesystem
```

Atlas بیشتر:

```text
references
orchestrates
observes
```

تا اینکه همه را مالک شود.

---

# 71. Security Boundary

Project metadata می‌تواند مسیر filesystem را ذخیره کند.

بنابراین باید:

```text
path privacy
URI permissions
sensitive environment metadata
repository credentials
```

با احتیاط مدیریت شوند.

Project metadata نباید secrets را به‌صورت پیش‌فرض ذخیره کند.

---

# 72. Secrets

مواردی مانند:

```text
SSH private keys
tokens
passwords
API keys
```

Project metadata نیستند.

اگر future secret management لازم شود، باید سیستم مستقلی داشته باشد.

---

# 73. Environment Variables

Environment variables ممکن است بخشی از execution context باشند، اما ذخیره دائمی همه environment variables خطرناک است.

Default:

```text
Do not persist arbitrary environment variables
```

مگر اینکه explicitly defined و safe باشند.

---

# 74. Project Settings

Project settings می‌تواند شامل preferenceهای غیرحساس باشد:

```text
preferred build action
default terminal directory
UI preferences
tooling hints
```

اما settings باید از runtime state جدا باشند.

---

# 75. Workspace Settings

Workspace settings نیز باید lightweight باشند:

```text
layout preference
last selected project
recent resources
```

جزئیات UI در UI Blueprint خواهد آمد.

---

# 76. Current Context Snapshot

Atlas می‌تواند یک snapshot مفهومی داشته باشد:

```text
ContextSnapshot
    workspace?
    project?
    location?
    file?
    repository?
    environment?
    session?
```

این snapshot برای Action resolution مفید است.

اما snapshot:

```text
≠ authoritative database truth
```

---

# 77. Context Freshness

Contextهای dynamic باید freshness semantics داشته باشند.

مثلاً:

```text
Git dirty state
Terminal CWD
Process state
Filesystem existence
```

ممکن است stale شوند.

Actionهای حساس باید refresh/revalidate کنند.

---

# 78. Project Root and Action Safety

Actionهایی مانند:

```text
Delete Project Files
```

نباید فقط با cached root اجرا شوند.

قبل از destructive execution:

```text
Resolve current root
Validate
Confirm target
Execute
Verify
```

این با Action Architecture هماهنگ است.

---

# 79. Workspace and Action Resolution

مثال:

```text
User:
"Build this project"

Workspace
  ├── Atlas
  └── Demo

Current Project = Atlas
```

Action resolver می‌تواند:

```text
BuildProject(project = Atlas)
```

بسازد.

اما اگر current project وجود نداشته باشد:

```text
Ask user
```

---

# 80. Workspace and AI Future

AI آینده می‌تواند context بگیرد:

```text
AI
 ↓
Context Snapshot
 ↓
Intent
 ↓
Action
```

اما AI نباید workspace state را بدون authorization تغییر دهد.

AI باید همان Action/Security boundaries را طی کند.

---

# 81. Workspace and Automation Future

Automation نیز می‌تواند Workspace/Project context داشته باشد.

مدل:

```text
Automation
 ↓
Resolved Context
 ↓
Actions
```

Automation نباید به UI navigation state وابسته باشد.

---

# 82. Project vs Workspace Decision Matrix

| Concern | Project | Workspace |
|---|---|---|
| Stable identity | Yes | Yes if persisted |
| Filesystem root | Primary reference | No ownership |
| Metadata | Yes | Lightweight |
| Git context | Reference/context | Can aggregate |
| Environment | Reference/context | Can aggregate |
| Terminal sessions | Associated | Can group |
| Active state | Can be active | Defines active grouping |
| Multi-project | N/A | Yes |
| File ownership | Semantic project scope | No |
| Deletion | Separate action | Removes grouping |
| Persistence | Yes | Optional/likely |
| UI screen | No | No |

---

# 83. Model A/B/C Revisited

Original options:

```text
A = Directory + Metadata
B = Directory + Tooling Context
C = Independent entity with multiple resources
```

Verdict:

```text
A alone
    → too weak

B
    → good baseline

C as full abstraction
    → premature
```

مدل منتخب:

> **Project = stable semantic identity + primary root reference + minimal metadata + optional tooling/repository/environment context.**

این مدل از B استفاده می‌کند ولی بدون ورود زودهنگام به complexity مدل C.

---

# 84. Workspace Decision

Workspace به‌عنوان:

> **Persistent or ephemeral grouping of Projects and active working context**

تعریف می‌شود.

Workspace مالک filesystem نیست.

Workspace می‌تواند چند Project را group کند.

Workspace implementation هنوز deferred است.

---

# 85. What Is Decided

تصمیمات این نسخه:

1. Project یک entity معنایی است، نه صرفاً directory.
2. ProjectId از root path مستقل است.
3. Project معمولاً یک primary root دارد.
4. Root می‌تواند missing/inaccessible باشد بدون اینکه identity حذف شود.
5. Project metadata باید minimal و purposeful باشد.
6. Project name از directory name مستقل است.
7. Repository بخشی از Project identity نیست.
8. Environment بخشی از Project identity نیست.
9. Project می‌تواند context/reference به repository و environment داشته باشد.
10. Workspace یک context/grouping است، نه filesystem root.
11. Workspace می‌تواند چند Project داشته باشد.
12. Project می‌تواند در چند Workspace قرار گیرد.
13. حذف Workspace نباید Project یا filesystem را حذف کند.
14. Forget Project از Delete Project Files جداست.
15. Dynamic state نباید به‌عنوان truth دائمی فرض شود.
16. Recent state source of truth نیست.
17. Session object و live process نباید مستقیماً persist شوند.
18. Project metadata نباید secrets را ذخیره کند.
19. Current default یک primary root است.
20. Multi-root/remote workspace/project فعلاً deferred است.
21. Persistence technology هنوز انتخاب نشده.
22. Workspace implementation هنوز deferred است.

---

# 86. Open Questions

1. ProjectId دقیقاً چگونه تولید و recover می‌شود؟
2. Reattach با چه evidenceای identity را حفظ می‌کند؟
3. آیا Project Type در نسخه Lite لازم است؟
4. چه metadataهایی واقعاً ارزش persistence دارند؟
5. RootReference دقیقاً چه schemaای دارد؟
6. URI/Path abstraction کجا قرار می‌گیرد؟
7. آیا یک Project می‌تواند root موقت داشته باشد؟
8. Nested Projects چگونه مدیریت می‌شوند؟
9. Monorepo detection چه semanticsی دارد؟
10. Workspace persistence در Lite لازم است؟
11. Workspace چند Project را تا چه حد پشتیبانی می‌کند؟
12. Session reattachment چگونه انجام می‌شود؟
13. Dynamic Git state چه freshness policy دارد؟
14. Environment context دقیقاً چه scopeای دارد؟
15. Project settings کجا ذخیره می‌شوند؟
16. Archive semantics دقیقاً چیست؟
17. آیا Project migration لازم است؟
18. آیا Project schema versioning از ابتدا لازم است؟
19. چه بخش‌هایی باید encrypted/secure storage باشند؟
20. آیا Workspace باید یک Action قابل اجرای مستقل باشد یا فقط context management باشد؟

---

# 87. Validation Gates

### Project

- [ ] Project identity independent from path validated.
- [ ] Root missing behavior validated.
- [ ] Reattach behavior validated.
- [ ] Project discovery behavior validated.
- [ ] Forget vs delete behavior validated.

### Filesystem

- [ ] Termux root validated.
- [ ] Android shared root validated.
- [ ] URI root validated where needed.
- [ ] Path/URI boundary validated.

### Repository

- [ ] Git context refresh validated.
- [ ] Repository ≠ Project behavior validated.
- [ ] Monorepo behavior explored.

### Workspace

- [ ] Single-project workspace validated.
- [ ] Multi-project workspace validated.
- [ ] Partial availability validated.
- [ ] Persistence/restoration validated.

### Security

- [ ] Secrets excluded from metadata.
- [ ] Destructive root operations revalidate target.
- [ ] Path/URI permissions handled correctly.

---

# 88. Architecture Drift Guards

Red Flag:

```text
Project = Directory only
```

Red Flag:

```text
Workspace = giant IDE state object
```

Red Flag:

```text
Workspace deletion = filesystem deletion
```

Red Flag:

```text
Project metadata = credentials vault
```

Red Flag:

```text
Cached Git state = authoritative state
```

Red Flag:

```text
Current UI screen = current Project
```

Red Flag:

```text
Termux path = universal filesystem path
```

---

# 89. Final Reference Model

```text
                         ATLAS
                           │
                     Context Model
                           │
              ┌────────────┴────────────┐
              ↓                         ↓
           Workspace                  Project
              │                         │
       ┌──────┼──────┐          ┌───────┼────────┐
       ↓      ↓      ↓          ↓       ↓        ↓
   Projects Sessions State      Root  Metadata  Context
       │                         │       │
       │                         │       ├── Repository?
       │                         │       └── Environment?
       │                         │
       └──────────────┬──────────┘
                      ↓
               Filesystem / URI
                      │
                 Termux / Android
```

اصل نهایی:

> **Project gives Atlas a stable semantic boundary around a real working resource. Workspace gives Atlas a flexible active context for one or more Projects.**

و:

> **Atlas owns project/workspace semantics and references; it does not automatically own the underlying filesystem, repository, environment, or live process.**

---

# 90. Next Step

با تثبیت این سند، ترتیب معماری به نقطه‌ای می‌رسد که می‌توان Blueprint قدیمی Lite را بازبینی کرد.

مرحله بعد:

```text
01_Core_Philosophy.md
        ↓
02_Capability_Model.md
        ↓
03_Action_Architecture.md
        ↓
04_Termux_Integration.md
        ↓
05_Project_Workspace.md
        ↓
06_Lite_Blueprint.md
        ↓
07_UI_Component_Blueprint.md
        ↓
08_Design_System.md
        ↓
09_Validation_Gates.md
        ↓
10_Technical_Implementation.md
        ↓
11_Roadmap_Sprints.md
        ↓
12_Atlas_Pro_Vision.md
```

Blueprint Lite باید اکنون بر اساس این چهار لایه بازسازی شود، نه اینکه صرفاً فایل قدیمی patch شود.

---

# Document Status

**Architecture Draft / Decision Candidate**

این سند بر اساس مدل‌های معماری قبلی Atlas و موارد صریحاً شناسایی‌شده در Handoff و Audit ساخته شده است.

مواردی که به‌عنوان Decision مشخص شده‌اند باید در Blueprintهای بعدی invariant فرض شوند، مگر اینکه contradiction واقعی یا evidence جدید باعث بازنگری شود.

موارد Open/Deferred نباید در implementation به‌صورت فرض قطعی وارد شوند.

**Next → Revision of Lite Blueprint**

**End of Document**
