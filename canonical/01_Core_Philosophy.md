# Atlas Core Philosophy & Product Definition

> سند: `01_Core_Philosophy.md`
> نسخه: 1.0-DRAFT
> تاریخ: 2026/08/27
> وضعیت: معماری مفهومی — پیش از توسعه
> نوع سند: Source of Truth برای هویت، مرزها و اصول بنیادی Atlas

---

## 1. هدف این سند

این سند هویت واقعی Atlas را قبل از اصلاح سایر Blueprintها تثبیت می‌کند.

هیچ Blueprint، Roadmap، UI specification، API design یا تصمیم فنی نباید با اصول این سند در تضاد باشد.

این سند عمداً وارد جزئیات implementation نمی‌شود. هدف آن پاسخ دادن به این پرسش‌هاست:

- Atlas دقیقاً چیست؟
- Atlas دقیقاً چه چیزی نیست؟
- چرا Atlas باید وجود داشته باشد؟
- رابطه Atlas با Termux چیست؟
- چه چیزهایی باید از CLI به GUI تبدیل شوند؟
- چه چیزهایی باید همچنان CLI باقی بمانند؟
- مرز abstraction در Atlas کجاست؟
- معیارهای اصلی تصمیم‌گیری در پروژه چیست؟

---

# 2. تعریف یک‌جمله‌ای Atlas

> **Atlas یک Native Android Graphical Shell و UX Layer برای اکوسیستم Termux/Linux است که قدرت command line را بدون مجبور کردن کاربر به تفکر و تعامل مستقیم با syntax فرمان‌ها، در قالب تجربه‌ای بصری، روان و کاربرپسند ارائه می‌کند.**

Atlas جایگزین Termux نیست.

Atlas یک Termux fork نیست.

Atlas یک Linux distribution نیست.

Atlas صرفاً یک Terminal emulator زیباتر نیست.

Atlas یک IDE صرفاً برای کدنویسی نیست.

Atlas یک wrapper از چند command ثابت نیز نیست.

Atlas یک **لایه تعامل گرافیکی روی قدرت موجود در Termux و ابزارهای Linux** است.

---

# 3. مدل ذهنی پروژه

مدل مفهومی Atlas به صورت زیر است:

```text
┌──────────────────────────────────────────────┐
│                 Atlas UI                     │
│                                              │
│ Home / Projects / Files / Tools / Terminal  │
│ Forms / Dialogs / Editors / Workflows        │
└──────────────────────┬───────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────┐
│              Atlas Intent Layer              │
│                                              │
│ Create Project                               │
│ Install Package                              │
│ Clone Repository                             │
│ Open File                                    │
│ Connect Server                               │
│ Run Tool                                     │
│ Search / Move / Copy / Delete                │
└──────────────────────┬───────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────┐
│               Atlas Action Engine            │
│                                              │
│ Validation / State / Resolution              │
│ Command Construction / Result Mapping        │
│ Error Mapping / Progress / Cancellation      │
└──────────────────────┬───────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────┐
│               Termux Adapter                 │
│                                              │
│ Official / Supported Integration Mechanisms  │
│ + Validated transport mechanisms             │
└──────────────────────┬───────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────┐
│              Termux / Linux                  │
│                                              │
│ Shell / Git / Python / Node / SSH / FFmpeg  │
│ Package Managers / CLI Tools / Scripts       │
└──────────────────────────────────────────────┘
```

این مدل یک اصل اساسی دارد:

> **Command، implementation است؛ Action، abstraction است.**

---

# 4. فلسفه اصلی: GUI when possible, CLI when necessary

Atlas نباید CLI را حذف کند.

Atlas باید نیاز به CLI را برای کارهای مناسب کاهش دهد.

بنابراین دو مسیر تعامل همزمان وجود دارد:

```text
                 ┌── GUI / Visual Interaction
                 │
User ── Atlas ───┤
                 │
                 └── Terminal / Direct CLI
```

GUI مسیر پیش‌فرض برای عملیات رایج و قابل مدل‌سازی است.

Terminal مسیر قدرت، انعطاف و escape hatch برای کاربران حرفه‌ای و عملیات غیرقابل abstraction است.

کاربر نباید برای انجام یک کار ساده syntax یک command را حفظ کند.

اما کاربر حرفه‌ای نباید به خاطر Atlas از قدرت کامل shell محروم شود.

---

# 5. مسئله‌ای که Atlas حل می‌کند

Command line فوق‌العاده قدرتمند است، اما برای استفاده روی موبایل چند مشکل ذاتی دارد:

- نیاز به حفظ syntax
- نیاز به تایپ زیاد
- دشواری مدیریت pathها
- دشواری کشف قابلیت‌ها برای کاربران جدید
- دشواری مشاهده وضعیت عملیات
- دشواری مدیریت فایل و پروژه با لمس
- دشواری انجام عملیات چندمرحله‌ای
- نیاز به تبدیل ذهنی بین «هدف» و «command»

Atlas این شکاف را هدف می‌گیرد.

مسئله Atlas این نیست که:

> «چطور یک Terminal زیباتر بسازیم؟»

مسئله واقعی این است:

> **«چطور قدرت command line را به تجربه‌ای تبدیل کنیم که کاربر Android بتواند با انتخاب، لمس، اسکرول، فرم و مشاهده وضعیت با آن کار کند؟»**

---

# 6. چیزی که Atlas باید برای کاربر پنهان کند

Atlas باید تا جای ممکن جزئیات implementation را از کاربر عادی پنهان کند.

نمونه:

کاربر می‌خواهد یک پروژه Python بسازد.

کاربر نباید مجبور باشد بداند:

```bash
mkdir myproject
cd myproject
python -m venv .venv
source .venv/bin/activate
```

Atlas باید تجربه‌ای شبیه این ارائه دهد:

```text
New Project
    ↓
Python
    ↓
Project Name
    ↓
Environment
    ↓
Create
```

در پشت صحنه ممکن است چند command اجرا شوند، اما commandها implementation هستند و نباید قرارداد اصلی UX باشند.

---

# 7. مثال دوم: Package Management

CLI:

```bash
pkg install python
```

Atlas:

```text
Packages
    ↓
Search: Python
    ↓
Python
    ↓
Install
```

Atlas مسئول ارائه تجربه است.

Termux/package manager مسئول اجرای واقعی عملیات است.

---

# 8. مثال سوم: Git

CLI:

```bash
git clone <repository>
cd <repository>
git checkout <branch>
```

Atlas می‌تواند این را به یک workflow تبدیل کند:

```text
Clone Repository
    ↓
Repository URL
    ↓
Destination
    ↓
Branch
    ↓
Clone
```

هدف حذف Git نیست.

هدف حذف اجبار به تعامل مستقیم با syntax Git برای عملیات رایج است.

---

# 9. مثال چهارم: عملیات فایل

CLI:

```bash
mv source.txt archive/source.txt
```

Atlas:

```text
Select File
    ↓
Move
    ↓
Select Destination
    ↓
Confirm
```

باز هم command زیرین مهم نیست؛ نتیجه و semantics مهم است.

---

# 10. مثال پنجم: ابزارهای پیچیده

Atlas نباید تلاش کند تمام CLI ecosystem را به UI تبدیل کند.

برای مثال، یک command پیچیده و کم‌استفاده ممکن است همچنان در Terminal اجرا شود.

در این شرایط Atlas باید بگوید:

> این عملیات abstraction مناسبی در UI ندارد؛ Terminal در دسترس است.

این شکست نیست.

این **مرزبندی سالم محصول** است.

---

# 11. اصل Semantic Abstraction

Atlas باید به جای commandها با مفهوم عملیات کار کند.

بد:

```text
Button → execute("git clone ...")
```

بهتر:

```text
Button
  ↓
CloneRepository Action
  ↓
Validation
  ↓
Command Resolution
  ↓
Termux Adapter
  ↓
Git
```

این تفاوت یکی از بنیادی‌ترین تصمیم‌های معماری Atlas است.

---

# 12. Action چیست؟

Action یک عملیات معنایی قابل اجرا در Atlas است.

نمونه‌های اولیه:

- CreateProject
- OpenProject
- OpenTerminal
- CreateFile
- CreateDirectory
- RenameFile
- MoveFile
- CopyFile
- DeleteFile
- InstallPackage
- RemovePackage
- UpdatePackage
- CloneRepository
- CommitChanges
- PullRepository
- PushRepository
- CreateBranch
- ConnectSSH
- RunTool
- RunScript

Action نباید به یک command خاص وابسته باشد، مگر در implementation layer.

---

# 13. Action Contract

هر Action در طراحی آینده باید حداقل این مفاهیم را مشخص کند:

```text
Action
├── Identity
├── Purpose
├── Inputs
├── Validation
├── Preconditions
├── Execution Strategy
├── Progress
├── Output
├── Error Mapping
├── Cancellation
└── Recovery
```

مثلاً:

```text
CreateProject

Inputs:
- name
- location
- template
- environment

Validation:
- valid name
- writable destination
- destination state

Execution:
- resolved by Action Engine

Output:
- project path
- created resources
- warnings

Errors:
- invalid name
- permission denied
- tool unavailable
- command failed
```

جزئیات دقیق این مدل در Blueprint جداگانه Action Architecture تعریف خواهد شد.

---

# 14. Atlas نباید یک Command Catalog باشد

Atlas نباید مجموعه‌ای از هزاران button برای commandهای مختلف باشد.

مثلاً این معماری اشتباه است:

```text
Button: ls
Button: pwd
Button: cd
Button: mkdir
Button: cp
Button: mv
Button: rm
...
```

این فقط یک GUI برای syntax است.

Atlas باید عملیات را بر اساس intent و context مدل کند.

مثلاً در File Manager:

```text
New Folder
New File
Copy
Move
Rename
Delete
Share
Open
Open Terminal Here
```

کاربر نیازی ندارد بداند پشت هرکدام چه commandی اجرا می‌شود.

---

# 15. Terminal جایگاه ویژه دارد

Terminal یک fallback نیست.

Terminal یک **Power Interface** است.

کاربر حرفه‌ای باید بتواند هر زمان خواست از GUI خارج شود و مستقیماً با shell کار کند.

همچنین Atlas باید تا حد امکان context را بین GUI و Terminal حفظ کند.

مثلاً:

```text
Project: MyApp
    ↓
Open Terminal
    ↓
Working Directory = /path/to/MyApp
```

و در جهت معکوس، Atlas در آینده می‌تواند contextهایی مثل مسیر جاری، project directory یا وضعیت repository را از session دریافت و در UI منعکس کند.

---

# 16. Project مفهوم مرکزی است

Project در Atlas نباید صرفاً یک پوشه در File Manager باشد.

در معماری آینده باید مشخص شود که Project می‌تواند شامل این contextها باشد:

- Root Path
- Project Type
- Environment
- Git State
- Related Terminal Sessions
- Recent Files
- User Preferences
- Tool Configuration
- Optional Metadata

اما تا زمانی که این مدل دقیق طراحی نشده، نباید فرض کنیم هر directory یک Project است.

---

# 17. Workspace مفهوم بالاتر از Project است

Workspace در آینده می‌تواند چند Project یا چند context مرتبط را در یک محیط کاری جمع کند.

نمونه:

```text
Workspace: Atlas Development
│
├── Project: Atlas App
├── Project: Documentation
└── Project: Scripts
```

این قابلیت برای نسخه‌های بعدی است و نباید Lite را به آن وابسته کنیم.

---

# 18. اصل Context Preservation

هر بار که کاربر بین GUI و CLI جابه‌جا می‌شود، Atlas باید تا حد امکان context را حفظ کند.

نمونه‌ها:

- Project context
- Working directory
- Selected file
- Git repository
- Active environment
- Active terminal session

هدف این است که کاربر احساس نکند دو سیستم جداگانه دارد.

---

# 19. رابطه Atlas و Termux

Termux در Atlas یک dependency/platform است، نه رقیب.

Atlas نباید:

- Termux را fork کند مگر ضرورت فنی اثبات‌شده وجود داشته باشد.
- shell را جایگزین کند.
- package manager را بازنویسی کند.
- رفتار ابزارهای Linux را تغییر دهد.
- filesystem داخلی Termux را بدون قرارداد معتبر دستکاری کند.
- برای قابلیت‌های موجود، implementation موازی غیرضروری بسازد.

Atlas باید تا حد ممکن از integrationهای رسمی و پایدار استفاده کند.

هر integration غیررسمی باید با Technical Spike و evidence فنی تأیید شود.

---

# 20. اصل عدم فرض API

هیچ API یا integration mechanism نباید فقط به دلیل وجود نام آن در یک سند قدیمی، قطعی فرض شود.

به‌خصوص موارد زیر باید قبل از نهایی شدن Architecture با مستندات و source code واقعی Termux اعتبارسنجی شوند:

- اجرای command از خارج Termux
- اجرای interactive session
- PTY transport
- session lifecycle
- دریافت live output
- ارسال stdin
- دسترسی به filesystem
- file sharing
- permissions
- background execution
- restore/reconnect

**هرجا سند قدیمی فرضی دارد، این سند آن فرض را تأییدشده تلقی نمی‌کند.**

---

# 21. اصل Integration First

قبل از ساخت UI جدی، یک Technical Spike باید ثابت کند که Atlas می‌تواند حداقل این زنجیره را reliably انجام دهد:

```text
Atlas
  ↓
Termux Integration
  ↓
Interactive Shell
  ↓
stdin
  ↓
stdout/stderr
  ↓
ANSI/Terminal Rendering
  ↓
Session Lifecycle
```

همچنین یک Spike جداگانه باید دسترسی فایل و عملیات غیرتعاملی را اعتبارسنجی کند.

تا زمانی که این دو بخش اثبات نشده‌اند، نباید معماری production را قفل کنیم.

---

# 22. GUI Abstraction Tiers

Atlas باید عملیات را حداقل در سه سطح مفهومی ببیند.

## Tier 1 — Native Visual Actions

عملیات رایج و قابل مدل‌سازی.

مثال:

- Create folder
- Rename
- Copy
- Move
- Install package
- Clone repository
- Create project

اینها باید UX گرافیکی کامل داشته باشند.

## Tier 2 — Guided Actions

عملیات پیچیده‌تر که با فرم یا wizard قابل ساده‌سازی هستند.

مثال:

- SSH setup
- Git merge
- Project environment setup
- Advanced tool configuration

## Tier 3 — Direct Terminal

عملیات بسیار تخصصی، ناشناخته، dynamic یا بدون abstraction مناسب.

مثال:

```bash
some-complex-cli --flag-a --flag-b ...
```

کاربر مستقیماً Terminal را استفاده می‌کند.

Atlas نباید برای Tier 3 UI مصنوعی تولید کند.

---

# 23. اصل Progressive Disclosure

Atlas نباید تمام پیچیدگی Linux را به‌صورت همزمان به کاربر نمایش دهد.

رابط باید از ساده به پیشرفته حرکت کند.

نمونه:

```text
Install Package
    ↓
Python
    ↓
Install
```

و برای کاربر حرفه‌ای:

```text
Advanced Options
    ↓
Repository
Version
Flags
Environment
```

این اصل جایگزین قانون قدیمی «همه چیز حداکثر سه Tap» است.

---

# 24. Tap Count قانون اصلی نیست

قانون قبلی:

> هر عملیات حداکثر ۳ Tap.

از این نسخه به بعد الزام معماری محسوب نمی‌شود.

معیار صحیح:

> **کمترین بار شناختی و کمترین اصطکاک برای انجام صحیح کار.**

گاهی سه Tap بهتر است.

گاهی پنج Tap با یک فرم واضح بهتر از سه Tap با یک command field است.

---

# 25. Zero Learning Curve — بازتعریف

Atlas نباید نیازمند آموزش برای عملیات رایج باشد.

اما این به معنی پنهان کردن همه قابلیت‌های پیشرفته نیست.

تعریف جدید:

> **کاربر تازه‌کار باید بتواند عملیات رایج را بدون دانستن Linux انجام دهد؛ کاربر حرفه‌ای باید بتواند به تمام قدرت CLI دسترسی داشته باشد.**

---

# 26. اصل User Intent over Syntax

Atlas باید به این سؤال فکر کند:

> کاربر چه می‌خواهد انجام دهد؟

نه:

> کاربر چه commandی باید بنویسد؟

این اصل باید در UX، Architecture و Action Engine تکرار شود.

---

# 27. اصل Native Android Experience

Atlas باید مانند یک Android application واقعی احساس شود.

نه مانند یک web page داخل WebView.

نه مانند Linux desktop کوچک‌شده.

نه مانند Termux با رنگ و آیکون جدید.

ویژگی‌های کلیدی:

- لمس و gesture طبیعی
- navigation استاندارد Android
- adaptive layout
- Material 3 در صورت سازگاری با هدف طراحی
- accessibility
- dark/light theme
- keyboard awareness
- one-hand usability
- سیستم back طبیعی Android
- lifecycle صحیح Android

---

# 28. Terminal Rendering بخشی از هویت Atlas است

هرچند Atlas Terminal-centric نیست، Terminal همچنان باید first-class باشد.

Terminal باید بتواند تجربه‌ای قابل اتکا برای:

- shell
- interactive CLI
- ANSI colors
- cursor
- selection
- copy/paste
- keyboard input
- resize
- session state
- foreground/background behavior

ارائه دهد.

جزئیات implementation بعداً در Architecture و Terminal Specification مشخص خواهد شد.

---

# 29. File System Philosophy

Atlas نباید filesystem را به یک abstraction مصنوعی تبدیل کند.

UI باید ساختار واقعی filesystem و محدودیت‌های Android/Termux را محترم بداند.

کاربر باید بتواند:

- مسیر را ببیند
- فایل‌ها را browse کند
- عملیات استاندارد انجام دهد
- فایل را باز کند
- Terminal را در context همان مسیر باز کند

هرگونه دسترسی به مسیرهای ایزوله Termux باید از integration معتبر و قابل پشتیبانی استفاده کند.

---

# 30. Error Philosophy

Atlas نباید خطای خام CLI را تنها پاسخ خود بداند.

مثلاً:

```text
Permission denied
```

برای کاربر عادی باید در صورت امکان تبدیل شود به:

```text
دسترسی به این مسیر امکان‌پذیر نیست.
مسیر یا مجوز مورد نیاز را بررسی کنید.

[View Details] [Open Terminal]
```

اما جزئیات فنی باید قابل مشاهده باقی بماند.

پس هر Error باید تا حد امکان دو لایه داشته باشد:

```text
Human-readable explanation
+
Technical details
```

---

# 31. هیچ abstraction بدون escape hatch

هر abstraction گرافیکی که Atlas ایجاد می‌کند باید راهی برای مشاهده جزئیات و ورود به Terminal داشته باشد، مخصوصاً در عملیات پیشرفته یا failure.

نمونه:

```text
Install Package Failed

[Retry]
[View Details]
[Open Terminal]
```

این باعث می‌شود GUI تبدیل به زندان کاربر حرفه‌ای نشود.

---

# 32. Performance Philosophy

Performance مهم است، اما اعداد فعلی مانند:

- Cold Start < 2s
- RAM < 50MB
- APK < 15MB

در این نسخه به عنوان قرارداد قطعی پذیرفته نمی‌شوند.

ابتدا باید baseline واقعی روی device classهای هدف اندازه‌گیری شود.

سپس Performance Budget تعیین می‌شود.

اصل ثابت:

> **Atlas نباید برای زیباتر شدن، responsiveness را قربانی کند.**

---

# 33. Scope Philosophy

Atlas نباید صرفاً چون یک قابلیت «جالب» است آن را وارد Core کند.

هر feature باید حداقل یکی از این ارزش‌ها را ایجاد کند:

1. کاهش نیاز به typing
2. کاهش cognitive load
3. بهبود discoverability
4. بهبود مدیریت context
5. بهبود مشاهده وضعیت
6. بهبود reliability
7. ایجاد workflow گرافیکی معنادار

اگر feature هیچ‌کدام را انجام نمی‌دهد، احتمالاً جای آن در Core Atlas نیست.

---

# 34. Core Atlas در برابر Pro Atlas

Lite/Core باید روی هسته تجربه تمرکز کند:

- Dashboard/Home
- Projects
- Files
- Terminal
- Sessions
- Command/Action history
- Bookmarks/shortcuts
- Settings
- Core visual workflows

Pro باید فقط زمانی اضافه شود که زیرساخت Core ثابت شده باشد.

Pro نباید تبدیل شود به:

> «هر ابزار توسعه‌ای که ممکن است روزی مفید باشد.»

هر قابلیت Pro باید از Core Action/Context architecture استفاده کند.

---

# 35. AI جایگزین معماری نیست

AI در آینده می‌تواند روی Action Layer سوار شود.

مثلاً:

```text
User:
"این repository را بگیر و dependencyهایش را نصب کن."

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

اما AI نباید مستقیماً کنترل نامحدود و بدون guardrail روی shell داشته باشد.

AI قابلیت آینده است، نه پایه معماری فعلی.

---

# 36. Plugin Philosophy

Plugin system تا زمانی که Action, Context و Permission model تثبیت نشده‌اند نباید طراحی production شود.

Plugin آینده باید بتواند در صورت نیاز قابلیت جدیدی به Atlas اضافه کند، اما نباید مجبور شود implementation داخلی Atlas را بشناسد.

هدف آینده:

```text
Plugin
  ↓
Atlas Action / Extension API
  ↓
Atlas Core
```

نه:

```text
Plugin
  ↓
Internal classes / hacks / direct Termux access
```

---

# 37. امنیت

Atlas در نهایت می‌تواند commandهای بسیار قدرتمند اجرا کند.

بنابراین اصل زیر غیرقابل مذاکره است:

> **UI زیبا نباید قدرت خطرناک را پنهان کند.**

برای عملیات destructive یا حساس باید در آینده این موارد در نظر گرفته شوند:

- confirmation
- scope visibility
- path visibility
- dry-run در صورت امکان
- permission model
- audit/history
- clear error state

نمونه بد:

```text
[Clean]
```

نمونه بهتر:

```text
Delete 184 files?

Path:
~/Projects/Test/cache

This action cannot be undone.

[Cancel] [Delete]
```

---

# 38. اصل عدم تغییر رفتار Linux

Atlas می‌تواند **روش تعامل** را تغییر دهد.

نباید **معنای ابزار زیرین** را تغییر دهد.

مثلاً اگر Git یک conflict دارد، Atlas نباید آن را پنهان کند و وانمود کند عملیات موفق بوده است.

Atlas باید وضعیت واقعی را نمایش دهد و در صورت امکان راهکار گرافیکی ارائه کند.

---

# 39. Atlas به عنوان Cockpit

یک مدل ذهنی مناسب برای پروژه:

```text
Linux / Termux
= Engine

Atlas
= Cockpit / Graphical Shell

Terminal
= Manual Control
```

کاربر می‌تواند با Cockpit کار کند.

وقتی لازم شد Manual Control را در اختیار دارد.

Engine همچنان همان Engine است.

---

# 40. چیزی که Atlas نیست

Atlas نباید به این پروژه‌ها تبدیل شود:

### Termux Fork
خیر.

### Linux Desktop
خیر.

### Full IDE
در Core خیر.

### Command launcher با صدها button
خیر.

### File Manager ساده
خیر.

### Terminal emulator صرفاً زیباتر
خیر.

### AI shell که همه چیز را خودکار انجام می‌دهد
در Core خیر.

### مجموعه‌ای از featureهای نامرتبط developer tools
خیر.

---

# 41. معیار تشخیص Feature مناسب

قبل از اضافه کردن هر feature این پرسش‌ها باید پاسخ داده شوند:

1. آیا یک نیاز واقعی کاربر Termux را حل می‌کند؟
2. آیا interaction را از syntax به intent نزدیک می‌کند؟
3. آیا واقعاً Android UX را بهتر می‌کند؟
4. آیا با Termux به‌عنوان backend سازگار است؟
5. آیا می‌توان آن را بدون fork کردن Termux پیاده کرد؟
6. آیا failure آن قابل توضیح و recovery است؟
7. آیا complexity آن ارزشش را دارد؟
8. آیا این feature به Core تعلق دارد یا Pro؟
9. آیا یک Action قابل تعریف برای آن وجود دارد؟
10. آیا Terminal همچنان escape hatch مناسبی باقی می‌ماند؟

اگر پاسخ چند سؤال مهم منفی باشد، feature باید بازنگری یا حذف شود.

---

# 42. اصل Evidence Before Architecture

در Atlas تصمیم‌های معماری باید تا حد امکان بر evidence متکی باشند.

منابع evidence می‌توانند شامل این موارد باشند:

- Termux official documentation
- Termux source code
- Android official documentation
- prototype / technical spike
- benchmark
- user testing
- real device testing
- issue tracker / community feedback

فرضیات بدون validation نباید تبدیل به قرارداد معماری شوند.

---

# 43. تعریف موفقیت Atlas

Atlas زمانی موفق است که یک کاربر بتواند کارهایی را که قبلاً فقط از طریق command line انجام می‌داد، در Android با تعامل گرافیکی انجام دهد، بدون اینکه قدرت و رفتار واقعی Termux/Linux را از دست بدهد.

موفقیت فقط به زیبایی UI وابسته نیست.

سه محور اصلی موفقیت:

```text
Usability
   ×
Power Preservation
   ×
Reliability
```

اگر یکی از این سه حذف شود، Atlas ناقص است.

---

# 44. معیارهای کیفی موفقیت

کاربر جدید:

> «می‌فهمم باید کجا بزنم.»

کاربر متوسط:

> «دیگر برای کارهای روزمره لازم نیست command حفظ کنم.»

کاربر حرفه‌ای:

> «اگر بخواهم، هنوز به shell کامل دسترسی دارم.»

کاربر Termux:

> «Atlas چیزی از قدرت محیط من کم نکرده؛ فقط استفاده از آن را راحت‌تر کرده.»

---

# 45. اصول غیرقابل مذاکره

این موارد باید در تمام Blueprintهای بعدی حفظ شوند:

1. Atlas جایگزین Termux نیست.
2. Atlas نباید بدون ضرورت Termux را fork کند.
3. Termux/Linux منبع execution است.
4. GUI مسیر اصلی عملیات رایج است.
5. Terminal مسیر کامل و بدون محدودیت برای power user باقی می‌ماند.
6. Intent مهم‌تر از command syntax است.
7. Action مهم‌تر از command implementation است.
8. هر abstraction باید escape hatch داشته باشد.
9. APIها باید قبل از تثبیت معماری validate شوند.
10. Featureها باید با هدف Atlas توجیه شوند.
11. Android UX باید native و responsive باشد.
12. Security و transparency نباید فدای simplicity شوند.
13. Core باید کوچک، منسجم و قابل اتکا بماند.
14. Pro نباید تبدیل به انبار feature شود.
15. هیچ Blueprint قدیمی صرفاً به دلیل قدیمی بودن الزام‌آور نیست.

---

# 46. چیزهایی که عمداً در این سند قفل نمی‌شوند

برای جلوگیری از تصمیم زودهنگام، موارد زیر در این نسخه تعیین نهایی نمی‌شوند:

- تکنولوژی دقیق Terminal transport
- API دقیق Termux integration
- روش دسترسی filesystem
- minSdk
- RAM budget
- APK size budget
- تعداد sessionهای همزمان
- database technology
- plugin implementation
- AI provider
- architecture جزئی کلاس‌ها
- package structure

این موارد باید در Blueprintهای تخصصی و Technical Spikeها بررسی و سپس تصویب شوند.

---

# 47. تغییرات نسبت به Blueprintهای قبلی

این سند چند فرض اصلی نسخه‌های قبلی را اصلاح می‌کند:

### اصلاح 1 — «زیباترین رابط برای Termux»

تعریف جدید:

> Graphical Shell / UX Layer برای استفاده بصری از قدرت Termux/Linux.

### اصلاح 2 — Command Bridge به عنوان مرکز معماری

Command Bridge دیگر نباید مرکز معنایی سیستم باشد.

Action/Intent Layer باید بالاتر از integration قرار گیرد.

### اصلاح 3 — سه Tap

«حداکثر سه Tap» از قانون سخت به یک heuristic UX تبدیل می‌شود.

### اصلاح 4 — Terminal به عنوان قلب کل محصول

Terminal دیگر کل محصول نیست؛ یکی از first-class interfaces محصول است.

### اصلاح 5 — Pro Feature Explosion

فهرست بزرگ Pro فعلاً به عنوان قرارداد محصول پذیرفته نمی‌شود.

### اصلاح 6 — API assumptions

تمام فرضیات مربوط به PTY، Socket، Session API و FileProvider نیازمند validation مستقل هستند.

---

# 48. وابستگی اسناد (به‌روزرسانی ۲۰۲۶/۱۰/۲۸)

این سند مبنای تمام اسناد canonical بعدی بوده است. بازطراحی کامل انجام شده:

```text
01_Core_Philosophy.md
        │
        ├── 02_Capability_Model.md        ✅ بازطراحی و تصویب‌شده
        │
        ├── 03_Action_Architecture.md     ✅ بازطراحی و تصویب‌شده
        │
        ├── 04_Termux_Integration.md      ✅ بازطراحی و تصویب‌شده
        │
        ├── 05_Project_Workspace.md       ✅ بازطراحی و تصویب‌شده
        │
        ├── 06_Lite_Blueprint.md          ✅ بازطراحی و تصویب‌شده
        │       (جایگزین archive/v01_01_Blueprint_Lite.md)
        │
        ├── 07_UI_Component_Blueprint.md  ✅ بازطراحی و تصویب‌شده
        │       (جایگزین archive/v01_03_UI_Design_Guide.md + archive/v01_04_Component_Library.md)
        │
        └── 12_Atlas_Pro_Vision.md        🔮 Future Vision — خارج از scope Lite
                (نسخه فعلی مرجع Pro؛ فایل منبع تاریخی در آرشیو فعلی موجود نیست)
```

تمام اسناد یادشده در `canonical/` موجودند.

---

# 49. تصمیم نهایی این نسخه

Atlas از این لحظه نباید به عنوان:

> «یک GUI برای Termux»

تعریف شود.

تعریف رسمی پروژه:

> **Atlas یک Graphical Shell و UX Layer برای Termux/Linux است که عملیات قابل فهم و تکرارشونده را از سطح command syntax به سطح intent، action و workflow گرافیکی منتقل می‌کند، در حالی که Termux و Linux همچنان موتور واقعی اجرا باقی می‌مانند و Terminal به عنوان مسیر کامل کنترل مستقیم حفظ می‌شود.**

این تعریف، مبنای تمام Blueprintهای بعدی است.

---

# 50. وضعیت سند

**Status:** DRAFT — برای تصویب معماری مفهومی

**Next required document:** `02_Capability_Model.md` ✅ تکمیل شده

Capability Model تعریف شده و مشخص می‌کند:

- Atlas چه قابلیت‌هایی را abstraction می‌کند.
- چه قابلیت‌هایی فقط guided هستند.
- چه قابلیت‌هایی مستقیماً به Terminal واگذار می‌شوند.
- مرز Lite و Pro کجاست.
- چه featureهایی خارج از scope هستند.

---

*پایان سند — Atlas Core Philosophy & Product Definition v1.0-DRAFT*
