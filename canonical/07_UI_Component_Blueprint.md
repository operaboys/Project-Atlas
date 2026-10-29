# Atlas — UI / Interaction / Component Blueprint

> سند: `07_UI_Component_Blueprint.md`
> Version: **1.0 — Architecture-Aligned Draft**
> Date: 2026-08-30
> Status: **Architecture / UX Contract Candidate**
> Depends on: `01_Core_Philosophy.md`, `02_Capability_Model.md`, `03_Action_Architecture.md`, `04_Termux_Integration.md`, `05_Project_Workspace.md`, `06_Lite_Blueprint.md`
> Purpose: تبدیل معماری تثبیت‌شده Atlas به مدل UI، Interaction و Component بدون اتصال business logic به UI.

---

# 1. Purpose

این سند مشخص می‌کند Atlas Lite از نظر تجربه کاربری چگونه باید معماری Core را مصرف کند.

هدف:

```text
User
 ↓
Intent
 ↓
UI interaction
 ↓
Action
 ↓
Result
 ↓
UI feedback
```

و جلوگیری از:

```text
UI
 ↓
raw command
 ↓
Termux
```

این سند design system کامل و pixel-perfect نیست.

این سند یک **interaction architecture** و **component contract** است.

---

# 2. Source-of-Truth Rule

این سند بر مبنای معماری تثبیت‌شده Atlas ساخته شده است.

مرزهای مهم:

```text
Core
    owns semantics

Action
    owns operation lifecycle

Adapter
    owns external integration

UI
    owns presentation + user interaction
```

UI نباید business rule را مالک شود.

---

# 3. Product UX North Star

Atlas باید به کاربر اجازه دهد:

```text
کمتر به syntax فکر کند
بیشتر روی نتیجه تمرکز کند
```

اما:

```text
قدرت CLI
کنترل
شفافیت
```

نباید از بین برود.

اصل:

> **Reduce cognitive load, not capability.**

---

# 4. Three Interaction Tiers

Atlas سه سطح تعامل دارد:

```text
Tier 1
Native Visual Action

Tier 2
Guided Workflow

Tier 3
Direct Terminal
```

Tier انتخابی باید بر اساس complexity و reliability باشد، نه صرفاً زیبایی UI.

---

# 5. Tier 1 — Native Visual Action

ویژگی:

```text
bounded
predictable
frequent
safe to model
```

نمونه:

```text
Rename File
Create Folder
Open Project
Install Known Package
```

UI باید semantic operation را ارائه کند.

---

# 6. Tier 2 — Guided Workflow

برای عملیات چندمرحله‌ای:

```text
input
validation
preview
execution
result
```

نمونه:

```text
Create Python Project
Clone Repository
Resize Video
Connect Server
```

Wizard نباید فقط command builder باشد؛ باید workflow معنایی باشد.

---

# 7. Tier 3 — Direct Terminal

برای:

```text
unknown commands
advanced shell usage
pipes
redirection
job control
custom scripts
interactive tools
```

Terminal مسیر رسمی و first-class است.

Atlas نباید کاربر Power User را مجبور به استفاده از abstraction ناقص کند.

---

# 8. Cognitive Load Principle

قانون قدیمی:

```text
Maximum 3 taps
```

به‌عنوان architecture rule حذف شده است.

قانون جدید:

> **Minimum Cognitive Load**

ممکن است:

```text
5 clear steps
```

از:

```text
2 ambiguous steps
```

بهتر باشد.

---

# 9. Navigation Model

Lite navigation اولیه:

```text
Home
Projects
Files
Terminal
Settings
```

Navigation باید ساده باشد.

Destinationها باید به semantic domains اشاره کنند، نه implementation modules.

---

# 10. Navigation Responsibilities

Home:

```text
entry point
recent context
quick actions
status
```

Projects:

```text
project discovery
creation
opening
management
```

Files:

```text
filesystem browsing
resource actions
```

Terminal:

```text
interactive CLI
sessions
output
```

Settings:

```text
preferences
integration
privacy
diagnostics
```

---

# 11. Navigation State

Navigation state:

```text
selected destination
navigation stack
modal state
```

است.

این state نباید source of truth برای:

```text
Project
Action
Filesystem
Session
```

باشد.

---

# 12. Active Context

UI باید در صورت امکان context جاری را قابل مشاهده نگه دارد:

```text
Workspace?
Project?
Location?
File?
Session?
```

اما فقط contextهایی را نمایش دهد که برای کار فعلی مفیدند.

---

# 13. Context Header

Context header می‌تواند:

```text
Atlas
Project Name
Current Location
```

را نمایش دهد.

در موبایل باید compact باشد.

اگر context ambiguous باشد، UI باید آن را نشان دهد.

---

# 14. Ambiguity UX

اگر Action target مشخص ندارد:

```text
Build Project
```

و چند Project موجود است:

```text
Select Project
```

نباید Atlas حدس بزند.

UX باید ambiguity را حل کند، نه پنهان.

---

# 15. Home Architecture

Home:

```text
Top
 ├── Current Context
 ├── Quick Actions
 ├── Recent Projects
 ├── Recent Actions
 └── Integration Status
```

ترتیب نهایی باید با usability testing تأیید شود.

---

# 16. Home — Current Context

اگر Project فعال باشد:

```text
Atlas Development
~/projects/atlas
```

نمایش داده شود.

اگر Project فعال نباشد:

```text
No project selected
```

و CTA مناسب:

```text
Open Project
Create Project
```

---

# 17. Home — Quick Actions

Quick actions باید از Action catalog بیایند.

مثال:

```text
Open Project
New Project
Open Terminal
Browse Files
```

نباید در UI command string hard-code شود.

---

# 18. Quick Action Contract

هر Quick Action:

```text
ActionId
Label
Icon
RequiredContext
Availability
Risk
```

را به‌صورت مفهومی دارد.

UI فقط presentation را کنترل می‌کند.

---

# 19. Disabled vs Hidden

اگر Action unavailable است:

```text
Disabled
```

بهتر از hide شدن است، وقتی دلیل availability برای کاربر مفید باشد.

مثلاً:

```text
Build
Requires a project
```

اما اگر feature اصلاً در Lite وجود ندارد:

```text
Do not show
```

---

# 20. Availability Explanation

Disabled action در صورت امکان باید دلیل داشته باشد:

```text
No project selected
Termux unavailable
Permission required
Tool not installed
```

این اطلاعات باید از Action/Capability state بیاید.

---

# 21. Projects UI

Project list:

```text
Project Card
 ├── Name
 ├── Availability
 ├── Location
 ├── Last Opened
 └── Overflow
```

Card نباید اطلاعات فنی غیرضروری نمایش دهد.

---

# 22. Project Card States

حداقل:

```text
Available
Missing Root
Inaccessible
Archived
```

و در صورت نیاز:

```text
Unknown
Refreshing
```

---

# 23. Project Card Actions

Context actions:

```text
Open
Rename
Reattach
Forget
```

Delete files باید جدا و visibly destructive باشد.

---

# 24. Project Creation

Screen:

```text
New Project
 ├── Name
 ├── Location
 ├── Type?
 ├── Options?
 └── Create
```

فرم باید progressive disclosure داشته باشد.

---

# 25. Project Creation Validation

Validation:

```text
Name valid?
Location accessible?
Conflict?
Required capability?
```

خطا باید inline و actionable باشد.

---

# 26. Project Type Selection

Type:

```text
Generic
Python
Node
Android
Web
Unknown
```

می‌تواند نمایش داده شود.

Unknown باید default امن باشد.

---

# 27. Project Open Flow

```text
Tap Project
 ↓
Resolve
 ↓
Validate root
 ↓
Refresh context
 ↓
Set active project
 ↓
Open Project context
```

UI نباید این lifecycle را خودش implement کند.

---

# 28. Missing Project Root

UI:

```text
Project unavailable
Root not found
```

Actions:

```text
Locate
Reattach
Forget
```

در صورت امکان:

```text
Retry
```

---

# 29. Forget Confirmation

Forget:

```text
Removes project from Atlas
Does not delete project files
```

این distinction باید در confirmation صریح باشد.

---

# 30. Delete Files Confirmation

Delete files باید:

```text
High-risk confirmation
Exact target
Consequence
Explicit confirm
```

داشته باشد.

مثلاً:

```text
Delete project files?

/path/to/project

This permanently removes files from storage.
```

---

# 31. Files UI

Filesystem browser:

```text
Top bar
 ├── Back
 ├── Location
 └── More

Content
 ├── Folders
 └── Files

Bottom / FAB
 ├── New File
 └── New Folder
```

Layout نهایی responsive خواهد بود.

---

# 32. Location Breadcrumb

در موبایل breadcrumb باید compact باشد:

```text
Atlas / scripts / tools
```

و در صورت کمبود فضا:

```text
... / tools
```

Tap روی location امکان navigation می‌دهد.

---

# 33. File Row

File row:

```text
Icon
Name
Optional metadata
Overflow
```

Tap:

```text
Open
```

Long press:

```text
Selection
```

---

# 34. Selection Mode

Selection mode باید native-feeling باشد.

```text
selected count
actions
cancel
```

Actions:

```text
Copy
Move
Rename
Delete
Share/Open
```

باید بر اساس resource type و capability availability محدود شوند.

---

# 35. File Operation Flow

مثلاً Rename:

```text
Select File
 ↓
Rename
 ↓
Input
 ↓
Validate
 ↓
Confirm if needed
 ↓
Action
 ↓
Result
```

UI فقط interaction را فراهم می‌کند.

---

# 36. File Conflicts

Copy/Move/Create operations ممکن است conflict داشته باشند.

UI باید semantic choice بدهد:

```text
Replace
Skip
Rename
Cancel
```

در صورت پشتیبانی Action.

نباید silently overwrite کند.

---

# 37. File Delete

Delete:

```text
Target
Consequence
Confirm
Action
Result
```

برای multiple selection:

```text
Delete 4 items?
```

باید count و scope مشخص باشد.

---

# 38. File Preview

Preview screen باید content-type-aware باشد.

Supported baseline:

```text
Text
Markdown
Image
Common metadata
```

Binary:

```text
Open with...
```

در صورت available بودن.

---

# 39. Text Viewer

Viewer:

```text
Search
Copy
Select
Word wrap
Encoding indication
```

ویرایشگر کامل Lite الزام نیست.

---

# 40. Terminal Destination

Terminal باید destination اصلی مستقل داشته باشد.

هدف:

> کاربر هرگز احساس نکند Terminal یک feature فرعی و محدودشده است.

---

# 41. Terminal Layout

Baseline:

```text
Top Bar
 ├── Session name
 ├── Status
 └── Session actions

Terminal Surface

Input / Keyboard interaction

Optional bottom controls
```

ترتیب دقیق با device testing مشخص می‌شود.

---

# 42. Terminal Surface

Terminal surface مسئول:

```text
rendering output
cursor
selection
scroll
input
```

است.

اما execution lifecycle را مالک نیست.

---

# 43. Terminal Session Indicator

نمایش حداقلی:

```text
Running
Exited
Disconnected
Unavailable
```

اگر process هنوز running باشد، status باید قابل فهم باشد.

---

# 44. Terminal Output

Output باید تا حد ممکن fidelity داشته باشد.

نباید:

```text
ANSI
stderr
control sequences
```

بدون دلیل حذف شوند.

Rendering layer می‌تواند آنها را interpret کند.

---

# 45. Terminal Input

Input باید:

```text
character input
enter
control keys
navigation
paste
```

را تا حد ممکن پشتیبانی کند.

Control behavior باید بر اساس session mechanism واقعی validate شود.

---

# 46. Terminal Keyboard

کیبورد native Android باید first-class باشد.

در صورت نیاز:

```text
Ctrl
Alt
Esc
Tab
Arrow
```

به‌صورت accessory row ارائه شود.

این row نباید فضای دائمی غیرضروری مصرف کند.

---

# 47. Terminal Gestures

Gestureهای مناسب:

```text
Scroll output
Select text
Copy
Paste
```

Gestures نباید با Android system gestures conflict ایجاد کنند.

---

# 48. Terminal Tabs

Tab support در صورت امکان:

```text
Session 1
Session 2
Session 3
```

باشد.

تعداد sessionها implementation constraint نیست تا benchmark انجام شود.

---

# 49. Session Creation

```text
New Terminal
 ↓
Resolve environment
 ↓
Create session
 ↓
Attach UI
```

اگر environment unavailable باشد:

```text
Explain
Retry
Settings / Integration
```

---

# 50. Session Closing

Close باید بین:

```text
Close UI
```

و:

```text
Terminate underlying process
```

تفاوت داشته باشد.

اگر close باعث termination می‌شود، consequence باید مشخص باشد.

---

# 51. Long-running Terminal Process

اگر process طولانی است:

```text
UI background
```

نباید الزاماً process را terminate کند.

اما lifecycle واقعی باید طبق Integration validation تعیین شود.

---

# 52. Action Screen

هر Guided Action می‌تواند structure زیر داشته باشد:

```text
Title
Description
Input
Options
Preview
Primary Action
Progress
Result
```

---

# 53. Form Architecture

Form fields باید:

```text
label
value
validation
helper
error
optional state
```

داشته باشند.

Placeholder نباید تنها label باشد.

---

# 54. Input Validation

Validation دو سطح:

```text
Local validation
```

و:

```text
Action-level validation
```

است.

مثلاً:

```text
empty name
```

را UI می‌تواند سریع تشخیص دهد.

اما:

```text
path writable?
tool available?
```

ممکن است نیازمند Action/Core validation باشد.

---

# 55. Progressive Disclosure

Form نباید همه optionها را از ابتدا نشان دهد.

Baseline:

```text
Essential fields
```

و:

```text
Advanced options
```

در expandable section.

اما advanced options نباید رفتار عملیات را پنهان کنند.

---

# 56. Preview Before Execute

برای عملیات مهم یا پیچیده، preview ترجیح داده می‌شود.

مثلاً:

```text
Create project

Location:
~/projects/demo

Environment:
Python venv

Dependencies:
requests
rich
```

و سپس:

```text
Create
```

---

# 57. Command Preview

Command preview می‌تواند برای Power User مفید باشد.

مثلاً:

```text
Generated execution
$ ...
```

اما:

```text
command = contract
```

نباید شود.

Preview باید توضیح دهد:

> Atlas برای اجرای این Action در backend چه کاری انجام خواهد داد.

---

# 58. Advanced / Technical Details

Technical detail باید progressive disclosure داشته باشد:

```text
Show details
```

و شامل:

```text
command
exit code
stderr
adapter diagnostics
```

باشد.

---

# 59. Progress UI

برای عملیات طولانی:

```text
Progress indicator
Current step
Elapsed time optional
Output / log
Cancel
```

نمایش داده شود.

---

# 60. Determinate vs Indeterminate

اگر progress واقعی وجود دارد:

```text
Determinate
```

در غیر این صورت:

```text
Indeterminate
```

نباید درصد جعلی نمایش داده شود.

---

# 61. Action Completion

موفقیت:

```text
Success
What happened
Next useful action
```

مثلاً:

```text
Project created
Open Project
Open Terminal
```

---

# 62. Partial Success

اگر Action partially succeeds:

```text
Partial success
Completed:
...
Failed:
...
```

باید نشان داده شود.

UI نباید آن را صرفاً Success یا Error نشان دهد.

---

# 63. Warning

Warning:

```text
operation completed
but with caveat
```

باید از failure جدا باشد.

---

# 64. Cancellation UX

Cancel:

```text
Cancel action?
```

فقط در صورت لازم confirmation می‌خواهد.

اگر operation already cancelling است:

```text
Cancelling...
```

نمایش داده شود.

---

# 65. Timeout UX

Timeout:

```text
Operation timed out
```

و options:

```text
Retry
View details
Open Terminal
Dismiss
```

در صورت semantic suitability.

---

# 66. Unknown State

اگر Atlas نمی‌تواند با اطمینان outcome را تعیین کند:

```text
Outcome unknown
```

نباید به‌صورت موفقیت نمایش داده شود.

UI باید recovery path بدهد:

```text
Inspect
Retry if safe
Check state
```

---

# 67. Error Presentation

Error component:

```text
What happened
Why
What can I do?
Technical details
```

ساختار:

```text
Human-readable
+
Actionable recovery
+
Optional technical details
```

---

# 68. Error Categories

UI taxonomy candidate:

```text
Permission
Invalid Input
Filesystem
Process
Network
Tool Missing
Integration
Cancelled
Timeout
Unknown
```

Taxonomy نهایی باید با Action Architecture تثبیت شود.

---

# 69. Snackbar

Snackbar برای:

```text
low-risk transient feedback
```

مثلاً:

```text
Copied
Renamed
Saved
```

مناسب است.

برای destructive یا critical errors کافی نیست.

---

# 70. Dialog

Dialog برای:

```text
confirmation
critical choice
small focused input
```

است.

نباید برای تمام notifications استفاده شود.

---

# 71. Bottom Sheet

Bottom sheet برای:

```text
context actions
filter
sort
secondary options
```

مناسب است.

برای workflowهای طولانی، screen/wizard بهتر است.

---

# 72. Confirmation Design

Confirmation باید بگوید:

```text
What will happen
What is affected
Whether it is reversible
```

و CTA باید semantic باشد.

بهتر:

```text
Delete files
```

بدتر:

```text
OK
```

---

# 73. Destructive CTA

Destructive operation نباید با primary neutral action visually indistinguishable باشد.

رنگ دقیق هنوز theme decision نیست.

اصل interaction:

```text
clear consequence
clear target
clear action
```

---

# 74. Empty States

Empty state باید:

```text
What is empty?
Why?
What can I do?
```

را پاسخ دهد.

مثلاً:

```text
No projects yet
Create your first project
```

---

# 75. Loading States

Loading باید context-specific باشد.

بد:

```text
Loading...
```

خوب:

```text
Checking project location...
```

در صورت داشتن اطلاعات واقعی.

---

# 76. Skeletons

Skeleton loading فقط وقتی ارزش دارد که:

```text
layout known
wait meaningful
```

باشد.

برای عملیات خیلی کوتاه، skeleton ممکن است flicker ایجاد کند.

---

# 77. Offline / Unavailable

اگر integration unavailable باشد:

```text
Atlas UI remains usable
```

اما Actionهای وابسته به integration:

```text
disabled / unavailable
```

با explanation.

---

# 78. Permission UX

Permission request باید نزدیک به action باشد.

مثلاً:

```text
Open Android storage
 ↓
Explain why access is needed
 ↓
Request access
```

نه اینکه همه permissionها در اولین launch درخواست شوند.

---

# 79. First Launch

First launch باید minimal باشد:

```text
Welcome
Termux status
Optional setup
Home
```

Atlas نباید onboarding طولانی تحمیل کند.

---

# 80. Termux Missing

اگر Termux نصب/available نیست:

```text
Atlas can still open
```

صفحه مناسب:

```text
Termux integration unavailable
Why it matters
How to resolve
Retry
```

جزئیات exact install flow باید طبق validated integration docs باشد.

---

# 81. Settings Architecture

Settings sections:

```text
Appearance
Terminal
Behavior
Privacy
Integration
Diagnostics
```

هر setting باید:

```text
user-facing
understandable
```

باشد.

---

# 82. Settings vs Project Settings

Global:

```text
Terminal font
Theme
Behavior
```

Project:

```text
Project-specific tooling hints
default directory
project preference
```

نباید این دو بی‌دلیل مخلوط شوند.

---

# 83. Search

Search در Atlas می‌تواند روی:

```text
Projects
Files
Actions
Settings
```

متمرکز باشد.

Universal search در نسخه اول ضروری نیست.

---

# 84. Command Palette

Command Palette می‌تواند در آینده یک interface بین GUI و Action catalog باشد.

مدل:

```text
Search Action
 ↓
Select
 ↓
Resolve Context
 ↓
Run Action
```

این با اصل same-action-multiple-interface سازگار است.

---

# 85. Accessibility

Baseline:

```text
Readable text
Touch targets
Content descriptions
Keyboard navigation where applicable
Contrast
Dynamic font support
```

مقادیر دقیق باید با Android accessibility guidance و testing تعیین شوند.

---

# 86. Touch Targets

کامپوننت‌های interactive باید اندازه مناسب لمس داشته باشند.

عدد دقیق implementation باید از Android/Material guidance و device testing گرفته شود.

---

# 87. Typography

Typography باید hierarchy واضح داشته باشد:

```text
Screen title
Section title
Body
Supporting
Error
Technical detail
```

فونت و scale دقیق فعلاً decision نهایی نیست.

---

# 88. Color Semantics

رنگ باید semantic باشد:

```text
Primary
Neutral
Success
Warning
Error
Info
```

نه اینکه هر feature palette مستقل بسازد.

Palette نهایی خارج از این سند است.

---

# 89. Icons

Icon باید:

```text
semantic
recognizable
consistent
```

باشد.

Icon نباید تنها راه فهم یک destructive action باشد.

---

# 90. Component Layering

Component architecture:

```text
Design Tokens
 ↓
Primitive Components
 ↓
Pattern Components
 ↓
Feature Components
 ↓
Screen
```

Business logic در هیچ‌کدام از UI layers قرار نمی‌گیرد.

---

# 91. Design Tokens

Candidate token categories:

```text
Color
Typography
Spacing
Shape
Elevation
Motion
Size
```

مقادیر دقیق deferred.

---

# 92. Primitive Components

Examples:

```text
AtlasButton
AtlasTextField
AtlasIconButton
AtlasCard
AtlasListItem
AtlasChip
AtlasDialog
AtlasSnackbar
AtlasProgress
```

این نام‌ها illustrative هستند و implementation naming contract نیستند.

---

# 93. Pattern Components

Examples:

```text
ProjectCard
FileRow
ActionForm
ErrorPanel
ProgressPanel
ContextHeader
TerminalToolbar
EmptyState
```

اینها reusable UX patterns هستند.

---

# 94. Feature Components

Examples:

```text
ProjectList
FileBrowser
TerminalSessionView
HomeDashboard
SettingsSection
```

Feature components می‌توانند ViewModel/state binding داشته باشند، اما domain semantics را مالک نیستند.

---

# 95. Screen Layer

Screen باید:

```text
collect UI state
render
send user events
navigate
```

کند.

Screen نباید:

```text
build shell command
manage process
delete files directly
```

کند.

---

# 96. UI Event Model

UI events:

```text
OnClick
OnSubmit
OnSelect
OnDismiss
OnRetry
OnCancel
OnRefresh
```

می‌توانند به presentation layer بروند.

Presentation layer آنها را به:

```text
Intent / Action request
```

تبدیل می‌کند.

---

# 97. State Model

UI state باید explicit باشد.

مثلاً:

```text
ProjectListState
 ├── Loading
 ├── Content
 ├── Empty
 └── Error
```

Action UI:

```text
Idle
Validating
Running
Success
PartialSuccess
Warning
Failure
Cancelled
Unknown
```

---

# 98. Unidirectional Flow

الگوی ترجیحی:

```text
UI
 ↓ Event
ViewModel / Presentation
 ↓ Intent
Action Engine
 ↓ Result / Events
Presentation State
 ↓
UI
```

این flow باید قابل trace باشد.

---

# 99. Navigation and Actions

Navigation action و domain action باید جدا باشند.

مثلاً:

```text
NavigateToProject
```

با:

```text
OpenProject
```

یکی نیست.

اولی UI navigation است.

دومی domain operation است.

---

# 100. Dialog and Action Lifecycle

Dialog فقط confirmation/input را جمع می‌کند.

مثلاً:

```text
Delete clicked
 ↓
Dialog
 ↓
Confirm
 ↓
DeleteFile Action
```

Dialog نباید خودش deletion را انجام دهد.

---

# 101. ViewModel Boundary

ViewModel می‌تواند:

```text
UI state
event handling
action invocation
```

را coordinate کند.

اما:

```text
filesystem API
Termux intent
shell command
```

نباید مستقیماً در ViewModel قرار بگیرد.

---

# 102. Repository Boundary

اگر repository/data layer وجود داشته باشد:

```text
UI
 ↓
Presentation
 ↓
Domain
 ↓
Repository / Adapter
```

اما Repository نباید صرفاً یک wrapper بی‌معنا برای همه چیز باشد.

---

# 103. Component State Ownership

هر state باید owner مشخص داشته باشد.

مثلاً:

```text
Dialog open
 → Screen/Presentation

Project metadata
 → Domain/Persistence

Terminal process
 → Runtime/Adapter

Filesystem truth
 → Filesystem
```

---

# 104. Avoid God Components

Red flag:

```text
AtlasScreen
```

که همه اینها را انجام دهد:

```text
navigation
database
Termux
filesystem
terminal
dialogs
actions
```

این component باید شکسته شود.

---

# 105. Avoid God ViewModel

همین برای:

```text
MainViewModel
```

که کل Atlas را کنترل کند.

ViewModel باید feature/context bounded باشد.

---

# 106. Reusable Action UI

Action UI باید بتواند metadata دریافت کند:

```text
title
description
fields
risk
progress
result
```

اما schema دقیق Action contract باید از Domain بیاید.

---

# 107. Dynamic Forms

Dynamic forms ممکن است در آینده از Action metadata ساخته شوند.

اما Lite v1 بهتر است برای عملیات مهم:

```text
typed feature-specific forms
```

داشته باشد.

چرا؟

چون dynamic UI کامل می‌تواند complexity و debugging را بالا ببرد.

---

# 108. Generic Action Renderer

Generic renderer:

```text
Action schema
 ↓
Generic form
```

برای نسخه فعلی:

```text
Candidate / future
```

است.

نباید Core را به UI schema وابسته کند.

---

# 109. Terminal and Native UI Boundary

Terminal باید داخل native app احساس شود، اما نباید به یک fake terminal تبدیل شود.

Native shell:

```text
Android chrome
+
real terminal surface
```

ترجیح داده می‌شود.

---

# 110. Visual Hierarchy

Atlas UI باید hierarchy زیر را حفظ کند:

```text
Where am I?
What can I do?
What is happening?
What happened?
What can I do next?
```

این hierarchy در همه major workflows قابل استفاده است.

---

# 111. Interaction Feedback

هر user action مهم باید feedback داشته باشد.

مثلاً:

```text
Tap Delete
 → confirmation

Start action
 → progress

Finish
 → result
```

Silent operations برای critical workflows مناسب نیستند.

---

# 112. Optimistic UI

Optimistic UI فقط برای operations کم‌ریسک و قابل rollback.

مثلاً:

```text
Rename
```

ممکن است optimistic باشد.

اما:

```text
Delete
Install Package
Run destructive command
```

بهتر است result-aware باشند.

---

# 113. Refresh Strategy

UI نباید بعد از هر interaction کل filesystem را scan کند.

Refresh باید:

```text
targeted
event-driven where possible
explicit when needed
```

باشد.

---

# 114. Stale State

اگر state احتمالاً stale است:

```text
refresh
```

قبل از عملیات حساس انجام شود.

مثلاً delete:

```text
cached file
≠
current file truth
```

---

# 115. Background Events

Long-running Actions می‌توانند event بدهند:

```text
Started
Progress
Output
Warning
Completed
Failed
```

Presentation layer باید آنها را به UI state تبدیل کند.

---

# 116. Notification Policy

Notifications خارج از Atlas screen باید محدود باشند.

برای عملیات طولانی و user-initiated:

```text
optional completion notification
```

ممکن است مناسب باشد.

Notification architecture هنوز deferred است.

---

# 117. Deep Links

Future:

```text
atlas://project/...
atlas://action/...
```

ممکن است useful باشد.

Lite v1 requirement نیست.

---

# 118. External Intents

Atlas ممکن است در آینده فایل یا URI از Android دریافت کند.

این باید به:

```text
Resource Reference
 ↓
Context
 ↓
Action
```

تبدیل شود.

UI نباید assumption کند که هر URI یک local path است.

---

# 119. Share Sheet

Share/Open actions می‌توانند native Android باشند.

اما security و URI permission باید رعایت شوند.

---

# 120. Project Context Indicator

در screenهایی که Project context مهم است، باید واضح باشد:

```text
Atlas Development
```

اما در screenهایی که context irrelevant است، نباید UI را شلوغ کند.

---

# 121. Workspace UI Stance

Workspace UI در Lite:

```text
Minimal / deferred
```

است.

اگر ارائه شود:

```text
Switch workspace
Add project
Remove project
```

کافی است.

Workspace manager کامل در scope فعلی نیست.

---

# 122. Multi-project UX

اگر چند Project context وجود دارد:

```text
Project selector
```

باید ساده و explicit باشد.

Atlas نباید به‌صورت silent بین projectها جابه‌جا شود.

---

# 123. Current Location vs Project Root

UI باید distinction را حفظ کند:

```text
Project:
~/projects/atlas

Current location:
~/projects/atlas/scripts
```

این برای Terminal و Files مهم است.

---

# 124. Terminal CWD Indicator

Terminal در صورت امکان می‌تواند CWD را نمایش دهد.

مثلاً:

```text
~/projects/atlas/scripts
```

اما این display باید از session state واقعی بیاید.

---

# 125. Project / Terminal Coupling

باز کردن Terminal از Project می‌تواند session را با Project context مرتبط کند.

اما:

```text
Terminal session ≠ Project
```

همچنان برقرار است.

---

# 126. Files / Terminal Coupling

از File Browser:

```text
Open in Terminal
```

می‌تواند:

```text
terminal CWD = file parent
```

را پیشنهاد کند.

اما این یک Action/context operation است، نه UI shortcut خام.

---

# 127. Action Availability Matrix

هر UI action باید availability داشته باشد:

```text
Available
Unavailable
Requires Context
Requires Capability
Requires Permission
Running
```

این stateها باید semantic باشند.

---

# 128. Action Risk Display

برای destructive operations:

```text
risk-aware copy
```

لازم است.

اما UI نباید برای هر operation هشدار قرمز نشان دهد.

Risk باید meaningful باشد.

---

# 129. Safe Defaults

Defaults باید:

```text
non-destructive
minimal
predictable
```

باشند.

مثلاً:

```text
Overwrite = off
Delete = explicit
Network = only when needed
```

---

# 130. Undo

Undo فقط وقتی نمایش داده شود که operation واقعاً reversible باشد.

نباید fake undo ارائه شود.

برای file operations future trash/recycle architecture ممکن است لازم باشد، اما فعلاً deferred است.

---

# 131. Recovery UI

Recovery باید context-aware باشد.

مثلاً:

```text
Permission error
 → Grant access

Tool missing
 → Install / Terminal

Network error
 → Retry

Unknown result
 → Inspect
```

---

# 132. Retry Safety

Retry button نباید generic باشد.

Action metadata باید مشخص کند retry safe است یا نه.

---

# 133. Accessibility and Error

Error فقط با رنگ نمایش داده نشود.

مثلاً:

```text
Error icon
Title
Message
Action
```

تا برای color-blind users و accessibility مناسب باشد.

---

# 134. Motion

Motion باید:

```text
fast
purposeful
non-blocking
```

باشد.

Animation نباید operation state را مخفی کند.

---

# 135. Terminal Motion

Terminal output نباید animation-heavy باشد.

Performance اولویت دارد.

---

# 136. Responsive Layout

Atlas باید حداقل برای:

```text
small phone
large phone
tablet / large screen
```

قابل استفاده باشد.

اما desktop IDE layout هدف Lite نیست.

---

# 137. Orientation

Portrait baseline است.

Landscape باید مخصوصاً برای Terminal و tablet usable باشد.

جزئیات هنوز نیازمند prototype validation است.

---

# 138. Material 3

Material 3 می‌تواند baseline visual system باشد.

اما:

```text
Material component
≠
Atlas UX
```

Atlas باید semantic patterns خودش را روی foundation مناسب بسازد.

---

# 139. Android Native Feel

نشانه‌های native:

```text
system back
Android keyboard
selection
dialogs/sheets
scroll behavior
accessibility
```

باید حفظ شوند.

---

# 140. Desktop-like Anti-patterns

از این موارد پرهیز:

```text
tiny click targets
dense toolbar everywhere
nested desktop menus
window-like floating panels everywhere
permanent sidebars on phone
```

Power user بودن به معنی desktop تقلیدی بودن نیست.

---

# 141. Power User Escape Hatch

در هر abstraction مهم، مسیر:

```text
Open Terminal
View Technical Details
```

در صورت relevance باید وجود داشته باشد.

---

# 142. Explainability

برای عملیات generated:

```text
What will Atlas do?
```

می‌تواند توضیح داده شود.

برای user:

```text
Action
Inputs
Expected result
```

مهم‌تر از command syntax است.

---

# 143. Command Transparency

برای Power User:

```text
Show command / technical details
```

مفید است.

اما command نباید تنها explanation باشد.

---

# 144. UI Does Not Own Truth

اگر UI می‌گوید:

```text
File exists
```

ولی filesystem state تغییر کرده است، UI باید قابل refresh باشد.

UI cache truth نیست.

---

# 145. Component Testing Boundary

Primitive components:

```text
visual + interaction tests
```

Pattern components:

```text
state + interaction
```

Feature components:

```text
presentation + action invocation
```

Domain behavior:

```text
domain tests
```

Integration:

```text
adapter tests / device tests
```

---

# 146. Screenshot Testing

Visual regression می‌تواند بعداً اضافه شود.

Lite v1 نباید development را صرفاً به screenshot matching تبدیل کند.

---

# 147. UI Performance

UI باید مراقب باشد:

```text
terminal output
large file list
large logs
```

را یکباره render نکند.

Virtualization/paging/streaming در implementation باید متناسب با واقعیات استفاده شود.

---

# 148. Large Terminal Output

Terminal باید بتواند output زیاد را handle کند بدون اینکه:

```text
entire history
```

به‌صورت نامحدود در memory نگه داشته شود.

Policy دقیق history deferred و benchmark-based است.

---

# 149. Large File Lists

File Browser باید برای directories بزرگ:

```text
incremental loading
sorting
filtering
```

را در صورت نیاز پشتیبانی کند.

---

# 150. Search Performance

Search در filesystem باید scope-aware باشد.

مثلاً:

```text
Current Folder
Project
Known Locations
```

نه الزاماً کل storage.

---

# 151. Component Naming

Naming باید semantic باشد.

خوب:

```text
ProjectCard
ActionProgress
ErrorPanel
```

بد:

```text
CommandCard
TermuxButton
ShellDeleteButton
```

وقتی component در اصل semantic Atlas operation را نشان می‌دهد.

---

# 152. Dependency Direction

UI dependencies:

```text
UI → Presentation → Domain
```

Integration:

```text
Domain → abstractions
Adapter → implementations
```

UI نباید:

```text
UI → Termux implementation
```

داشته باشد.

---

# 153. Compose Boundary

اگر Jetpack Compose استفاده شود:

```text
Composable
```

باید mostly stateless/presentation-focused باشد.

Stateful wrappers می‌توانند state collection و event dispatch را انجام دهند.

---

# 154. Composable Anti-pattern

بد:

```text
Button {
    termux.run("rm -rf ...")
}
```

خوب:

```text
Button {
    onEvent(DeleteProjectClicked)
}
```

سپس:

```text
Presentation
 → Action
```

---

# 155. UI Model Mapping

Domain models را نباید بدون دلیل مستقیم در UI render کرد.

ترجیح:

```text
Domain Model
 ↓
UI Model
 ↓
Composable
```

این اجازه می‌دهد UI مستقل‌تر بماند.

---

# 156. Error Model Mapping

Domain error:

```text
PermissionDenied
```

UI:

```text
Storage access is required
[Grant access]
```

Technical details:

```text
Show details
```

این mapping باید deterministic باشد.

---

# 157. Localization

تمام user-facing strings باید localization-ready باشند.

Command output و technical logs ممکن است localization نشوند.

---

# 158. RTL

اگر Atlas چندزبانه باشد:

```text
RTL
LTR
```

باید در layout architecture لحاظ شوند.

نباید صرفاً text alignment عوض شود؛ directional icons و navigation نیز باید بررسی شوند.

---

# 159. Bilingual / Multilingual

UI text باید از resource system بیاید.

Domain identifiers:

```text
ActionId
ProjectId
```

نباید localized باشند.

---

# 160. Date / Time

Recent state و timestamps باید مطابق locale/user settings نمایش داده شوند.

Storage format مستقل از locale باشد.

---

# 161. Technical Output Language

Terminal output نباید توسط Atlas ترجمه شود.

Atlas می‌تواند:

```text
UI explanation
```

را localized کند.

اصل:

```text
User-facing Atlas text → localized
Tool output → preserved
```

---

# 162. Copy Technical Details

کاربر باید بتواند technical output/error را copy کند.

این برای debugging و support مهم است.

---

# 163. Support / Diagnostics

Diagnostics screen می‌تواند:

```text
Atlas version
Termux availability
Integration status
Relevant capabilities
Recent error
```

را نمایش دهد.

نباید secrets یا sensitive command input را نمایش دهد.

---

# 164. Diagnostics Export

Future:

```text
Export diagnostic report
```

ممکن است وجود داشته باشد.

قبل از export:

```text
Review sensitive data
```

لازم است.

---

# 165. Security UX

UI باید تفاوت بین:

```text
Read
Write
Execute
Delete
Network
```

را در عملیات مهم در نظر بگیرد.

کاربر نباید با security theatre مواجه شود.

---

# 166. Arbitrary Shell

Terminal arbitrary shell access را فراهم می‌کند.

GUI Actions نباید بدون reason به arbitrary shell تبدیل شوند.

اگر operation فقط از طریق shell قابل انجام است:

```text
Guided / Terminal
```

بسته به complexity.

---

# 167. GUI vs Terminal Decision

برای هر feature:

```text
Is the semantic contract stable?
        ↓
Are inputs bounded?
        ↓
Is result predictable?
        ↓
Does GUI reduce cognitive load?
```

اگر نه:

```text
Terminal-first
```

---

# 168. Screen Inventory — Lite

Baseline screens:

```text
HomeScreen
ProjectsScreen
ProjectDetails / ContextScreen
NewProjectScreen
FilesScreen
FilePreviewScreen
TerminalScreen
SettingsScreen
DiagnosticsScreen
```

ممکن است بعضی از اینها در implementation ادغام شوند.

---

# 169. Modal Inventory

```text
ConfirmDialog
InputDialog
ProjectPicker
LocationPicker
ActionOptionsSheet
ContextMenu
```

---

# 170. Shared Patterns Inventory

```text
TopBar
ContextHeader
SearchBar
EmptyState
ErrorState
LoadingState
ProgressPanel
ResultPanel
```

---

# 171. Navigation Graph — Candidate

```text
Home
 ├── Projects
 │    ├── Project
 │    └── New Project
 │
 ├── Files
 │    └── File Preview
 │
 ├── Terminal
 │    └── Session
 │
 └── Settings
      └── Diagnostics
```

Action flows می‌توانند از هر destination شروع شوند.

---

# 172. Project-Centric Flow

```text
Home
 ↓
Project
 ↓
Context
 ├── Files
 ├── Terminal
 └── Actions
```

---

# 173. File-Centric Flow

```text
Files
 ↓
Folder
 ↓
File
 ├── Preview
 ├── Edit later
 ├── Share/Open
 └── Actions
```

---

# 174. Action-Centric Flow

```text
Home / Command Palette / Project / Files
 ↓
Action
 ↓
Form
 ↓
Validation
 ↓
Preview
 ↓
Running
 ↓
Result
```

این flow یکی از core UX patterns Atlas است.

---

# 175. Terminal-Centric Flow

```text
Terminal
 ↓
Session
 ↓
Input / Output
 ↓
Command
 ↓
Process
 ↓
Result
```

Terminal از Action abstraction فرار نمی‌کند؛ اما arbitrary shell interaction ذاتاً می‌تواند direct باشد.

---

# 176. Cross-Surface Action

یک Action مثل:

```text
Open Terminal Here
```

می‌تواند از:

```text
Files
Project
Home
```

فراخوانی شود.

Action یکی است؛ UI entry points متفاوت‌اند.

---

# 177. Same Action Principle

```text
GUI
 ─┐
   ├── Action
CLI bridge
 ─┤
AI future
 ─┤
Automation future
 ─┘
```

این architecture باید حفظ شود.

---

# 178. Event Traceability

برای debugging باید بتوان فهمید:

```text
User event
 → Intent
 → Action
 → Adapter
 → Result
```

اما logs نباید sensitive data را leak کنند.

---

# 179. UI Analytics

Product analytics فعلاً:

```text
Deferred
```

است.

اگر آینده اضافه شود، باید privacy-aware باشد.

---

# 180. No Hidden Telemetry

Atlas نباید command output، secrets یا terminal input را برای analytics جمع‌آوری کند.

---

# 181. First Prototype Priority

Prototype UI نباید همه screenها را بسازد.

اول باید این vertical slice ثابت شود:

```text
Home
 ↓
Create/Open Project
 ↓
Project Context
 ↓
Open Terminal
 ↓
Run command
 ↓
See result
```

این slice بیشترین architecture risk را زود آشکار می‌کند.

---

# 182. Second Prototype Slice

بعد:

```text
Project
 ↓
Files
 ↓
Create/Rename/Delete
 ↓
Result
```

---

# 183. Third Prototype Slice

بعد:

```text
Guided Action
 ↓
Form
 ↓
Progress
 ↓
Result / Error
```

---

# 184. Prototype Success

Prototype موفق نیست چون:

```text
UI pretty
```

است.

موفق است اگر:

```text
Intent
Action
Context
Adapter
Result
```

واقعاً end-to-end کار کنند.

---

# 185. UI Acceptance Criteria

برای هر major workflow:

```text
[ ] User understands current context
[ ] Inputs are clear
[ ] Validation is understandable
[ ] Operation state is visible
[ ] Errors are actionable
[ ] Consequences are clear
[ ] Terminal escape hatch exists where relevant
[ ] UI does not contain backend-specific assumptions
```

---

# 186. Architecture Acceptance Criteria

```text
[ ] No UI → raw shell execution
[ ] No screen-owned business logic
[ ] No UI dependency on Termux transport
[ ] Project identity remains separate from path
[ ] Workspace remains context/grouping
[ ] Domain state separate from UI state
[ ] Dynamic state can be refreshed
[ ] Destructive actions revalidate targets
```

---

# 187. UX Anti-Patterns

نباید:

```text
Button → shell command
```

یا:

```text
Every task → wizard
```

یا:

```text
Every task → terminal
```

باشد.

Atlas باید balanced باشد.

---

# 188. Abstraction Anti-Pattern

اگر UI فقط command flags را به شکل TextFieldهای متعدد نمایش دهد:

```text
-v
-i
-o
--foo
--bar
```

این GUI واقعی نیست.

این:

```text
CLI with boxes
```

است.

---

# 189. Fake Simplicity

UI نباید complexity را فقط به مرحله بعد هل دهد.

مثلاً:

```text
Create
```

و بعد:

```text
Unknown failure
```

بدتر از یک workflow واضح با validation است.

---

# 190. Hidden Consequences

Atlas نباید:

```text
Delete
Overwrite
Install
Network operation
```

را بدون اطلاع مناسب انجام دهد.

---

# 191. Over-Disclosure Anti-pattern

در طرف دیگر:

```text
command
PID
exit code
filesystem inode
adapter state
```

نباید روی هر screen نمایش داده شود.

Progressive disclosure لازم است.

---

# 192. UI Contract Summary

UI owns:

```text
Presentation
Interaction
Navigation
Accessibility
User-facing feedback
```

UI does not own:

```text
Filesystem truth
Process lifecycle
Termux transport
Action semantics
Project persistence
```

---

# 193. Component Contract Summary

Primitive:

```text
visual + interaction
```

Pattern:

```text
reusable UX semantics
```

Feature:

```text
feature presentation
```

Screen:

```text
composition + navigation
```

Domain:

```text
business semantics
```

Adapter:

```text
external system
```

---

# 194. Decision Table

| Concern | UI | Presentation | Domain/Action | Adapter |
|---|---|---|---|---|
| Button rendering | Yes | No | No | No |
| User event | Yes | Yes | No | No |
| Intent mapping | No | Yes | Yes | No |
| Validation | Basic UX | Coordination | Authoritative | Integration |
| Action lifecycle | No | Observe | Yes | Execute |
| Termux transport | No | No | Abstract | Yes |
| Filesystem truth | No | No | Abstract | Yes |
| Project semantics | Display | Coordinate | Yes | Persistence/adapter |
| Error mapping | Render | Map | Define | Supply details |
| Progress | Render | Observe | Produce | Emit |
| Navigation | Render/trigger | Coordinate | No | No |

---

# 195. Implementation Naming Guard

نام‌گذاری باید architecture را منعکس کند.

ترجیح:

```text
Action
Project
Resource
Session
Context
```

نه:

```text
CommandButton
TermuxManager
ShellHelper
GlobalController
```

مگر اینکه واقعاً implementation concern باشند.

---

# 196. Architecture Review Checklist

قبل از merge هر UI feature:

```text
[ ] What is the user intent?
[ ] What Action represents it?
[ ] What Capability does it require?
[ ] What Context does it require?
[ ] Where is authoritative validation?
[ ] What result states exist?
[ ] What errors exist?
[ ] Is cancellation relevant?
[ ] Is the operation destructive?
[ ] What is the recovery path?
[ ] Does UI know too much about backend?
```

---

# 197. Scope Guard

اگر یک UI feature نیاز دارد:

```text
new global state
new transport
new direct shell API
new persistence model
```

باید قبل از implementation architecture review شود.

ممکن است feature واقعی باشد؛ اما ممکن است symptom یک abstraction غلط هم باشد.

---

# 198. Open Questions

این سند عمداً موارد زیر را نهایی نمی‌کند:

```text
Exact color palette
Exact typography
Exact spacing scale
Exact navigation component
Exact terminal renderer
Exact PTY mechanism
Exact session transport
Exact persistence technology
Exact minSdk
Exact notification strategy
Exact workspace UI
```

اینها باید با evidence/prototype/UX validation تعیین شوند.

---

# 199. Validation Plan

مرحله بعدی validation باید حداقل شامل:

```text
Device usability
Terminal interaction
Filesystem performance
Large output
Large directory
Keyboard behavior
Back navigation
Permission flows
Error flows
Long-running actions
Rotation / process recreation
Dark/light mode
Accessibility
```

باشد.

---

# 200. Final UI Architecture

```text
                    USER
                      │
                  Interaction
                      ↓
              ┌───────────────┐
              │   Atlas UI    │
              │ Compose/etc.  │
              └───────┬───────┘
                      ↓
                Presentation
                      ↓
                    Intent
                      ↓
                   Action
                      ↓
             Capability + Context
                      ↓
                 Validation
                      ↓
                  Execution
                      ↓
               Adapter Layer
                      ↓
                Termux/Linux
                      ↓
               Result / Events
                      ↓
                Presentation
                      ↓
                     UI
```

---

# 201. Final UX Principle

> **Atlas should make Linux feel native on Android without making Linux powerless.**

سه اصل عملیاتی:

```text
GUI when possible.
Guided when useful.
CLI when necessary.
```

و سه اصل معماری:

```text
Action over command.
Context over hidden assumptions.
Result over black box.
```

---

# 202. Final Component Architecture

```text
Design Tokens
      ↓
Primitives
      ↓
Patterns
      ↓
Feature Components
      ↓
Screens
      ↓
Presentation
      ↓
Actions
      ↓
Adapters
```

هیچ فلشی نباید به عقب برگردد به شکل:

```text
Composable → raw Termux API
```

---

# 203. Final Decision Set

تصمیم‌های این سند:

1. UI مستقیماً command اجرا نمی‌کند.
2. UI مستقیماً Termux transport را نمی‌شناسد.
3. Action abstraction مرز اصلی بین intent و execution است.
4. Navigation action با domain action یکی نیست.
5. Project context باید explicit و قابل مشاهده باشد.
6. Ambiguity نباید با حدس پنهان شود.
7. Three-tier interaction model حفظ می‌شود.
8. Minimum Cognitive Load جایگزین Maximum 3 Taps می‌شود.
9. Terminal first-class باقی می‌ماند.
10. Guided workflows برای عملیات چندمرحله‌ای استفاده می‌شوند.
11. Primitive / Pattern / Feature / Screen layers جدا هستند.
12. Domain truth از UI state جداست.
13. Dynamic state باید refreshable باشد.
14. Destructive actions باید consequence و target روشن داشته باشند.
15. Error UI باید actionable باشد.
16. Unknown outcome نباید success فرض شود.
17. Progress جعلی ممنوع است.
18. Technical details باید progressive disclosure داشته باشند.
19. Project و Workspace در UI یکی فرض نمی‌شوند.
20. Current location و Project root جدا هستند.
21. Session و Project یکی نیستند.
22. Accessibility از ابتدا بخشی از component architecture است.
23. Localization-ready بودن از ابتدا رعایت می‌شود.
24. Exact visual tokens هنوز lock نشده‌اند.
25. Exact Android/Termux implementation هنوز lock نشده است.
26. Prototype باید vertical-slice و architecture-first باشد.

---

# 204. Next Step

بعد از این سند، پروژه باید وارد مرحله‌ای شود که architecture را با prototypeهای واقعی validate کند.

ترتیب پیشنهادی:

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
07_UI_Component_Blueprint.md (this document)
        ↓
08_Design_System.md
        ↓
09_Validation_Gates.md
        ↓
10_Technical_Implementation.md
        ↓
11_Roadmap_Sprints.md
```

مرحله بعدی نباید فوراً به full implementation تبدیل شود.

اول باید **Validation & Prototype Gates** مشخص شوند تا assumptions فنی و UX در device واقعی آزمایش شوند.

---

# Document Status

**Architecture / UX Contract Candidate**

این سند Blueprint اجرایی pixel-perfect نیست و implementation details اثبات‌نشده را قفل نمی‌کند.

موارد `Candidate`, `Deferred`, `Open Question` و `Needs Validation` نباید در implementation به‌عنوان fact قطعی تلقی شوند.

**End of Document**
