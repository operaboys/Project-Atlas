# Atlas — Validation & Prototype Gates

> سند: `09_Validation_Gates.md`
> Version: **1.0**
> Status: **Validation Contract / Pre-Implementation Gate**
> Date: 2026-08-31
> Depends on: `01_Core_Philosophy.md`, `02_Capability_Model.md`, `03_Action_Architecture.md`, `04_Termux_Integration.md`, `05_Project_Workspace.md`, `06_Lite_Blueprint.md`, `07_UI_Component_Blueprint.md`

---

## 1. Purpose

این سند مشخص می‌کند قبل از ورود Atlas به implementation جدی، کدام فرضیه‌ها باید در دنیای واقعی آزمایش شوند، هر prototype چه چیزی را ثابت یا رد می‌کند، معیار قبولی چیست، و چه چیزهایی هنوز نباید به‌عنوان تصمیم قطعی وارد کد شوند.

اصل مرکزی:

> **No critical architecture assumption becomes implementation truth before it survives a relevant prototype or evidence review.**

این سند جایگزین architecture نیست؛ مکانیزم اعتبارسنجی architecture است.

## 2. Validation Philosophy

Atlas نباید صرفاً با این معیار ارزیابی شود:

```text
Builds successfully
```

معیار واقعی:

```text
Architecture
    ↓
Prototype
    ↓
Observed behavior
    ↓
Evidence
    ↓
Decision
    ↓
Implementation
```

اگر prototype فرضیه‌ای را رد کرد، معماری باید اصلاح شود؛ نه اینکه prototype را با workaround به زور موفق جلوه دهیم.

## 3. Evidence Levels

هر تصمیم validation باید یکی از این وضعیت‌ها را داشته باشد:

```text
ASSUMPTION   هنوز آزمایش نشده
PROTOTYPED   نمونه ساخته شده، نتیجه ثبت شده
VALIDATED    معیارهای پذیرش پاس شده
CONDITIONAL  فقط در شرایط مشخص معتبر است
REJECTED     فرضیه شکست خورده
DEFERRED     فعلاً ارزش آزمایش ندارد
```

`PROTOTYPED` به معنی `VALIDATED` نیست.

## 4. Validation Gates

```text
G0 — Scope & Risk Gate
 ↓
G1 — Termux Reality Gate
 ↓
G2 — Execution / Action Gate
 ↓
G3 — Project / Filesystem Gate
 ↓
G4 — Terminal UX Gate
 ↓
G5 — Long-running / Lifecycle Gate
 ↓
G6 — Error / Recovery Gate
 ↓
G7 — Performance Gate
 ↓
G8 — Android UX / Accessibility Gate
 ↓
G9 — Architecture Integrity Gate
 ↓
G10 — Implementation Readiness Gate
```

هیچ Gate پایین‌تر نباید شکست یک Gate بنیادی‌تر را پنهان کند.

## 5. G0 — Scope & Risk Gate

هدف: قبل از prototype مشخص شود:

```text
What are we testing?
Why?
What can fail?
What decision depends on it?
```

هر Test Card باید داشته باشد:

```text
ID
Hypothesis
Risk
Prototype
Environment
Inputs
Expected Result
Failure Signals
Acceptance Criteria
Evidence
Decision
Follow-up
```

Risk classes:

```text
P0 — Architecture blocker
P1 — High implementation risk
P2 — Important UX/performance risk
P3 — Nice-to-know
```

Pass:

```text
[ ] Critical assumptions listed
[ ] Unknowns separated from decisions
[ ] Test owners identified
[ ] Acceptance criteria measurable
[ ] No hidden implementation commitment
```

## 6. G1 — Termux Reality Gate

هدف: اثبات integration واقعی Atlas با Termux روی device هدف.

محیط باید ثبت شود:

```text
Android version
Device class
Termux version
Termux installation source
Shell
Architecture
Storage model
Permission state
Battery/background restrictions
```

سؤالات:

```text
Can Atlas reliably launch the intended Termux integration path?
Can Atlas detect availability?
Can Atlas receive useful execution results?
Can interactive sessions remain usable?
What survives app backgrounding?
What survives process recreation?
What happens when Termux is unavailable?
```

Pass:

```text
[ ] Availability can be detected reliably
[ ] Failure is distinguishable from success
[ ] Integration errors are observable
[ ] No undocumented transport assumption remains
[ ] Real device evidence exists
```

## 7. G2 — Execution / Action Gate

هدف: اثبات اینکه Action abstraction واقعاً قابل اجراست و UI مجبور نیست command-centric شود.

Vertical slice:

```text
UI
 ↓
Intent
 ↓
Action
 ↓
Validation
 ↓
Execution
 ↓
Adapter
 ↓
Termux
 ↓
Result
 ↓
UI
```

حداقل سه Action واقعی برای prototype انتخاب شود، مانند:

```text
CreateDirectory
WriteFile
RunCommand
```

stateهای مورد آزمایش:

```text
Idle
Validating
Running
Success
Failure
Cancelled
Unknown
```

در صورت پشتیبانی واقعی:

```text
Progress
PartialSuccess
Warning
```

Pass:

```text
[ ] UI never directly executes transport
[ ] Action has explicit identity
[ ] Inputs are validated
[ ] Result is structured
[ ] Error is distinguishable
[ ] Cancellation semantics are known
[ ] Unknown outcome is representable
```

## 8. G3 — Project / Filesystem Gate

هدف: اثبات:

```text
Project Identity
≠
Filesystem Root
≠
Current Location
```

سناریوهای Project:

```text
Create project
Open project
Close/reselect project
Root moved
Root missing
Root inaccessible
Project metadata survives restart
```

سناریوهای filesystem:

```text
Create
Rename
Move
Copy
Delete
Conflict
Permission failure
Large directory
Stale state
```

Critical test:

```text
Project ID = stable identity
Root = changeable reference
```

اگر تغییر یا inaccessible شدن root باعث نابودی identity شود، مدل implementation باید بازبینی شود.

Pass:

```text
[ ] Project identity survives root changes
[ ] Missing root is representable
[ ] Forget does not imply file deletion
[ ] Delete is explicit
[ ] Conflicts are observable
[ ] Current location is separate from root
```

## 9. G4 — Terminal UX Gate

هدف: اثبات usability واقعی Terminal روی Android.

آزمایش:

```text
Text input
Enter
Backspace
Arrow keys
Tab
Ctrl
Esc
Copy
Paste
Scroll
Selection
ANSI output
stderr
long output
interactive process
```

Keyboard:

```text
Normal keyboard
Accessory control row
IME behavior
Back navigation
Keyboard open/close
Landscape
```

Fidelity:

```text
ANSI colors
cursor
line wrapping
control sequences
stderr/stdout distinction
scroll behavior
```

Performance در baseline:

```text
small output
medium output
large output
continuous output
```

ثبت شود:

```text
frame drops
input latency
memory growth
scroll latency
CPU load
```

Pass:

```text
[ ] Input is reliable
[ ] Essential control keys work
[ ] Paste/copy works
[ ] Interactive command remains usable
[ ] Output rendering is stable
[ ] No severe UI jank
[ ] No uncontrolled memory growth
```

## 10. G5 — Long-running / Lifecycle Gate

سناریوها:

```text
App foreground
App background
Screen navigation
Screen recreation
Rotation
Process recreation
Termux still running
Termux process exits
Device lock/unlock
```

برای عملیات طولانی:

```text
Start
 ↓
Background app
 ↓
Wait
 ↓
Return
 ↓
Observe
```

ثبت شود:

```text
process alive?
state recoverable?
result recoverable?
duplicate execution?
```

همچنین عمداً یک Unknown Outcome ایجاد شود. Atlas نباید:

```text
unknown → success
```

فرض کند.

Pass:

```text
[ ] Lifecycle behavior is documented
[ ] No accidental duplicate execution
[ ] Running state can be represented
[ ] Unknown outcome is explicit
[ ] Recovery strategy exists
```

## 11. G6 — Error / Recovery Gate

حداقل failureها:

```text
Invalid input
Permission denied
Missing tool
Missing project root
Network failure
Process failure
Timeout
Cancellation
Integration unavailable
Unknown outcome
```

Error contract:

```text
What happened
Why
What can user do
Technical detail
```

برای هر Retry:

```text
Is retry safe?
Can it duplicate side effects?
Does state need refresh?
```

Pass:

```text
[ ] Errors are not generic where avoidable
[ ] Recovery path exists
[ ] Destructive retry is not automatic
[ ] Technical details can be inspected
[ ] Unknown is not mislabeled
```

## 12. G7 — Performance Gate

Benchmark areas:

```text
App startup
Project loading
Directory listing
File operations
Action dispatch
Terminal startup
Terminal rendering
Large output
Memory
Battery impact
```

هر benchmark باید ثبت کند:

```text
device
OS
build
data size
warm/cold state
measurement method
result
```

تصمیم performance باید بر اساس evidence باشد، نه حدس.

Pass:

```text
[ ] No catastrophic memory behavior
[ ] No unacceptable interaction latency
[ ] Large output remains usable
[ ] Large directories remain usable
[ ] Performance constraints are evidence-based
```

## 13. G8 — Android UX / Accessibility Gate

آزمایش:

```text
First launch
Navigation
Back
Dialogs
Sheets
Forms
Validation
Empty states
Loading
Errors
Dark/light
Large text
```

Accessibility:

```text
TalkBack/basic screen reader behavior
Content descriptions
Touch targets
Text scaling
Contrast
Focus order
Keyboard accessibility where relevant
```

در صورت چندزبانه بودن:

```text
LTR
RTL
long strings
short strings
numeric/path content
```

Pass:

```text
[ ] Core flows usable with accessibility features
[ ] No critical information relies on color alone
[ ] Large text does not destroy primary workflows
[ ] Back behavior is predictable
[ ] Localization does not break layout
```

## 14. G9 — Architecture Integrity Gate

بعد از prototypeها باید بررسی شود implementation آزمایشی architecture را دور نزده باشد.

Forbidden shortcuts:

```text
Composable → Termux transport
Screen → shell command
ViewModel → raw shell execution
UI → filesystem mutation
UI → process lifecycle
Project path used as Project ID
```

مگر با exception صریح و مستند.

Dependency audit:

```text
UI
 ↓
Presentation
 ↓
Domain
 ↓
Abstractions
 ↓
Adapters
```

حداقل یک Action end-to-end trace شود:

```text
User tap
 ↓
UI event
 ↓
Intent
 ↓
Action
 ↓
Capability/context
 ↓
Validation
 ↓
Adapter
 ↓
Execution
 ↓
Result
 ↓
UI state
```

Pass:

```text
[ ] Dependency direction holds
[ ] No accidental bypass
[ ] Action identity survives full flow
[ ] Context remains explicit
[ ] Adapter boundary is real
[ ] UI remains transport-agnostic
```

## 15. G10 — Implementation Readiness Gate

شرایط:

```text
[ ] Core architecture validated
[ ] Termux integration validated enough
[ ] Action lifecycle validated
[ ] Project identity validated
[ ] Terminal baseline validated
[ ] Lifecycle risks understood
[ ] Critical error paths validated
[ ] Major performance risks measured
[ ] UI architecture survives prototype
[ ] No critical open blocker
```

خروجی Gate:

```text
GO
GO WITH CONDITIONS
RETURN TO ARCHITECTURE
STOP / RESEARCH
```

`GO WITH CONDITIONS` باید conditionهای دقیق داشته باشد.

## 16. Prototype Order

```text
P1 — Termux handshake / availability
        ↓
P2 — Minimal Action execution
        ↓
P3 — Project + filesystem
        ↓
P4 — Terminal interaction
        ↓
P5 — Lifecycle / long-running
        ↓
P6 — Error / recovery
        ↓
P7 — Performance
        ↓
P8 — UI/accessibility
        ↓
P9 — Architecture audit
```

## 17. Prototype Rules

Full app قبل از integration validation ساخته نشود.

UI polishing قبل از Terminal reality انجام نشود.

Database، PTY، session limit، minSdk و memory budget تا وقتی evidence ندارند lock نشوند.

Prototype موفق محسوب نمی‌شود فقط چون happy path کار کرده است.

Screenshot به‌تنهایی evidence معماری نیست.

هر prototype باید report داشته باشد:

```text
Test ID
Date
Device
Environment
Build
Steps
Observed
Expected
Result
Logs
Screenshots if useful
Decision
```

هر failure:

```text
ID
Gate
Severity
Symptom
Root Cause
Workaround
Architectural Impact
Decision
Status
```

و هر decision:

```text
Decision
Evidence
Alternatives rejected
Confidence
Next review condition
```

## 18. What Must Not Be Locked Yet

تا پایان validation این موارد نباید بدون evidence قطعی شوند:

```text
Exact Termux transport
Exact PTY mechanism
Exact persistence technology
Exact minSdk
Exact session limit
Exact memory budget
Exact APK size target
Exact terminal history policy
Exact background execution strategy
Exact visual token values
```

## 19. What Should Be Locked

این اصول باید architecture-level باقی بمانند:

```text
Action over raw command
Context over hidden assumptions
Project identity over path identity
UI separate from execution
Adapter boundary
Explicit result states
Unknown outcome is not success
Destructive operations are explicit
Terminal remains first-class
Evidence before irreversible implementation choices
```

## 20. Minimal End-to-End Acceptance

قبل از implementation گسترده، این flow باید روی device واقعی قابل اثبات باشد:

```text
Open Atlas
 ↓
Create/Open Project
 ↓
Project becomes active
 ↓
Open Terminal in project context
 ↓
Run a command
 ↓
Observe output
 ↓
Return to Files
 ↓
Create/modify resource
 ↓
Observe result
 ↓
Trigger one failure
 ↓
Recover
```

## 21. Final Gate

Atlas آماده implementation گسترده است اگر:

```text
Architecture is coherent
+
Critical integration assumptions are validated
+
Terminal is usable
+
Project model survives real filesystem conditions
+
Lifecycle behavior is known
+
Failures are recoverable
+
Performance risks have evidence
+
UI does not violate architecture
```

اگر یکی از P0ها شکست بخورد:

```text
Implementation does not proceed as if validated.
```

## 22. Final Principle

> **Prototype to discover truth, not to prove the design right.**

اول reality.

بعد commitment.

بعد implementation.

## 23. Next Step

پس از این سند، مرحله بعدی **Technical Implementation Blueprint** است؛ اما فقط بر پایه تصمیم‌هایی که Gateها یا evidence معتبر آن‌ها را پشتیبانی کرده‌اند.

موارد unresolved باید با وضعیت خودشان باقی بمانند:

```text
Validated
Conditional
Deferred
Open
```

تا architecture به‌دلیل زود کدنویسی کردن به technical debt تبدیل نشود.

---

## Document Status

**VALIDATION CONTRACT / PRE-IMPLEMENTATION GATE**

این سند معیار عبور Atlas از Architecture به Implementation است.

**End of Document**
