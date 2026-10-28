# Atlas — Roadmap & Sprint Breakdown

> سند: `11_Roadmap_Sprints.md`
> Version: **1.0**
> Status: **Execution Roadmap — Architecture Aligned**
> Date: 2026-09-01
> Depends on: Atlas Architecture through `10_Technical_Implementation.md`

---

## 1. Purpose

این سند معماری و implementation blueprintهای Atlas را به یک مسیر اجرایی قابل مدیریت تبدیل می‌کند.

هدف:

```text
Architecture
    ↓
Validation
    ↓
Implementation
    ↓
Vertical Slice
    ↓
Hardening
    ↓
Release
```

این Roadmap صرفاً فهرست featureها نیست. هر Sprint باید خروجی قابل مشاهده، قابل تست و قابل تصمیم‌گیری ایجاد کند.

اصل:

> **Every Sprint must reduce uncertainty or increase validated capability.**

---

## 2. Roadmap Philosophy

Atlas نباید با تعداد featureهای ساخته‌شده اندازه‌گیری شود.

معیار پیشرفت:

```text
Validated architecture
+
Working vertical slices
+
Reduced critical risk
+
Observable quality
```

پس:

```text
More code ≠ More progress
```

---

# H0 — Reality & Validation

## Sprint 0 — Validation Preparation

### Goal
آماده‌سازی محیط و Test Cardها برای Prototypeها.

### Deliverables

```text
Validation matrix
Environment record
Risk register update
Test cards P1–P9
Device baseline
```

### Dependencies

```text
09_Validation_Gates.md
```

### Exit Criteria

```text
[ ] P0/P1 assumptions identified
[ ] Device environment recorded
[ ] Acceptance criteria measurable
[ ] No hidden implementation decision
```

---

## Sprint 1 — Termux Reality

### Goal
اثبات integration واقعی Atlas با Termux.

### Deliverables

```text
Termux availability detection
Minimal integration prototype
Launch/communication test
Failure behavior
Evidence report
```

### Risks

```text
Transport incompatibility
Permission restrictions
Lifecycle limitations
Installation/environment differences
```

### Gate

```text
G1 — Termux Reality Gate
```

### Exit Criteria

```text
[ ] Availability detectable
[ ] Integration path reproducible
[ ] Success observable
[ ] Failure observable
[ ] Transport choice evidence-backed
```

اگر شکست خورد:

```text
RETURN TO ARCHITECTURE
```

---

## Sprint 2 — Action Reality

### Goal
اثبات Action abstraction روی implementation واقعی.

### Deliverables

```text
Action identity
ExecutionContext
ActionResult
Validation
Adapter boundary
Minimal execution path
```

### Suggested Actions

```text
CreateDirectory
WriteFile
RunCommand
```

### Gate

```text
G2 — Execution / Action Gate
```

### Exit Criteria

```text
[ ] UI does not execute transport
[ ] Action identity explicit
[ ] Result structured
[ ] Failure explicit
[ ] Cancellation semantics known
[ ] Unknown outcome representable
```

---

## Sprint 3 — Project Reality

### Goal
اثبات Project identity و filesystem model.

### Deliverables

```text
Project identity
Root reference
Project repository abstraction
Filesystem abstraction
Create/open project
Missing root state
```

### Critical Test

```text
Project ID remains stable
even when root changes or becomes unavailable.
```

### Gate

```text
G3 — Project / Filesystem Gate
```

---

## Sprint 4 — Terminal Reality

### Goal
اثبات usability Terminal روی Android واقعی.

### Deliverables

```text
Terminal session prototype
Input
Output
Control keys
Scroll
Copy/Paste
Interactive command
```

### Gate

```text
G4 — Terminal UX Gate
```

### Exit Criteria

```text
[ ] Input reliable
[ ] Essential controls work
[ ] Interactive session usable
[ ] Rendering stable
[ ] No severe jank
[ ] No uncontrolled memory growth
```

---

## Sprint 5 — Lifecycle & Recovery

### Goal
اثبات behavior عملیات طولانی، lifecycle و failure recovery.

### Deliverables

```text
Background/foreground tests
Process recreation tests
Cancellation
Unknown outcome
Retry policy
Failure mapping
Recovery flows
```

### Gates

```text
G5 — Lifecycle
G6 — Error / Recovery
```

---

## Sprint 6 — Reality Review

### Goal
جمع‌بندی H0 و تصمیم برای ورود به implementation گسترده.

### Deliverables

```text
Validation summary
Open Decisions Register
Architecture changes
Updated ADRs
Performance baseline plan
Implementation readiness decision
```

### Gate

```text
G10 — Implementation Readiness Gate
```

### Possible Outcomes

```text
GO
GO WITH CONDITIONS
RETURN TO ARCHITECTURE
STOP / RESEARCH
```

---

# H1 — Core Foundation

## Sprint 7 — Repository Skeleton

### Goal
ساخت skeleton واقعی پروژه بدون feature sprawl.

### Deliverables

```text
Gradle setup
Application shell
Package/module structure
Dependency rules
Test infrastructure
Build variants
```

### Definition of Done

```text
[ ] Clean build
[ ] Tests execute
[ ] Dependency direction established
[ ] No architecture bypass
```

---

## Sprint 8 — Core Domain Contracts

### Goal
پیاده‌سازی قراردادهای stable domain.

### Deliverables

```text
IDs
Project
ExecutionContext
Action
Capability
ActionResult
DomainError
SideEffect classification
```

### Tests

```text
Domain unit tests
Invariant tests
Result-state tests
```

---

## Sprint 9 — Action Engine

### Goal
ساخت engine برای dispatch و اجرای Actionها.

### Deliverables

```text
Intent dispatch
Action validation
Context resolution
Execution
Result mapping
Cancellation
```

### Critical Rule

```text
UI → Intent → Action
```

نه:

```text
UI → command
```

### Exit Criteria

```text
[ ] Fake adapter works
[ ] Real contract tested
[ ] Failure path tested
[ ] Unknown outcome tested
```

---

## Sprint 10 — Project & Filesystem Core

### Goal
ساخت Project و filesystem subsystem.

### Deliverables

```text
ProjectRepository
Project identity
Root reference
Active project context
Filesystem port
Filesystem adapter
```

### Tests

```text
Create
Open
Rename
Move
Copy
Delete
Conflict
Permission failure
Missing root
```

---

## Sprint 11 — Termux Adapter

### Goal
پیاده‌سازی transport تاییدشده در adapter boundary.

### Deliverables

```text
Termux adapter
Availability
Execution
Result translation
Error translation
Resource cleanup
```

### Rule

Transport types نباید از adapter بیرون نشت کنند.

---

# H2 — First Production Vertical Slice

## Sprint 12 — Terminal Core

### Goal
ساخت terminal subsystem production-oriented بر اساس prototype.

### Deliverables

```text
TerminalSession
Lifecycle
Input channel
Output stream
Error stream
Close
Recovery
```

### Tests

```text
Session lifecycle
Input/output
Process exit
Failure
Cancellation
Unknown
```

---

## Sprint 13 — Vertical Slice A

### Goal
اولین مسیر end-to-end واقعی.

### Flow

```text
Create/Open Project
 ↓
Set Active Project
 ↓
Open Terminal
 ↓
Run Safe Action
 ↓
Receive Result
 ↓
Reflect Result in UI
```

### Success Condition

این Sprint باید architecture را در یک مسیر واقعی ثابت کند، نه اینکه صرفاً چند screen بسازد.

---

## Sprint 14 — Files Integration

### Goal
اتصال filesystem به vertical slice.

### Flow

```text
Project
 ↓
Files
 ↓
Create/Modify Resource
 ↓
Observe Result
 ↓
Terminal
```

### Tests

```text
Mutation
Conflict
Failure
Refresh
Stale state
```

---

## Sprint 15 — Error & Recovery UX

### Goal
تبدیل failureهای domain به UX قابل اقدام.

### Deliverables

```text
Error presentation
Retry
Cancel
Unknown outcome UI
Technical detail inspection
Recovery states
```

---

## Sprint 16 — Lifecycle Hardening

### Goal
تثبیت behavior در شرایط واقعی Android.

### Tests

```text
Background
Foreground
Navigation
Recreation
Lock/unlock
Termux process exit
Long-running operation
```

---

# H3 — Expansion & Hardening

## Sprint 17 — Core Navigation & Home

### Goal
ساخت shell اصلی Atlas.

### Deliverables

```text
Home
Projects
Files
Terminal
Settings entry points
Navigation state
```

UI باید architecture را consume کند، نه اینکه architecture را bypass کند.

---

## Sprint 18 — Project UX

### Goal
تجربه کامل Project management.

### Deliverables

```text
Create
Open
Rename
Forget
Missing root
Project selection
Project context display
```

`Forget` نباید به‌صورت ضمنی delete filesystem باشد.

---

## Sprint 19 — Files UX

### Goal
تجربه کامل Files.

### Deliverables

```text
Browse
Open
Create
Rename
Move
Copy
Delete
Conflict handling
Empty states
Loading
Errors
```

---

## Sprint 20 — Terminal UX Expansion

### Goal
تکمیل Terminal بر اساس نیاز واقعی، نه feature inflation.

Potential:

```text
History
Session management
Improved keyboard controls
Search
Output navigation
```

هر مورد فقط پس از اثبات ارزش وارد شود.

---

## Sprint 21 — Accessibility & Localization

### Goal
تثبیت UX برای accessibility و چندزبانه بودن.

### Tests

```text
TalkBack
Large text
Focus order
Touch targets
Contrast
LTR
RTL
Long strings
Path/numeric content
```

### Gate

```text
G8 — Android UX / Accessibility Gate
```

---

## Sprint 22 — Performance

### Goal
بهینه‌سازی بر اساس measurement.

### Benchmark

```text
Startup
Project loading
Directory listing
File operations
Action dispatch
Terminal startup
Terminal rendering
Large output
Memory
Battery
```

### Rule

```text
Measure → Optimize → Measure
```

---

## Sprint 23 — Security & Reliability Hardening

### Goal
کاهش riskهای release.

### Focus

```text
Input validation
Path safety
Process argument safety
Permission handling
Safe logging
Secret avoidance
Resource cleanup
Destructive operation safeguards
```

---

## Sprint 24 — Architecture Audit

### Goal
بازبینی کامل implementation در برابر architecture.

### Audit

```text
Dependency graph
Action boundaries
Context propagation
Adapter isolation
State ownership
Lifecycle ownership
Error semantics
UI isolation
```

### Gate

```text
G9 — Architecture Integrity Gate
```

---

## Sprint 25 — Release Candidate

### Goal
ساخت اولین Release Candidate واقعی.

### Required

```text
Critical E2E
Termux integration
Project lifecycle
Terminal
Filesystem
Failure/recovery
Accessibility baseline
Performance baseline
Release configuration
```

---

# 4. Critical Path

```text
Termux Reality
      ↓
Action Reality
      ↓
Project Reality
      ↓
Terminal Reality
      ↓
Lifecycle / Recovery
      ↓
Implementation Readiness
      ↓
Core Contracts
      ↓
Action Engine
      ↓
Project / Filesystem
      ↓
Termux Adapter
      ↓
Terminal Core
      ↓
First Vertical Slice
```

اگر هر مرحله بحرانی شکست بخورد، feature expansion نباید آن را پنهان کند.

---

# 5. Parallel Work

کارهای زیر می‌توانند parallel باشند، به شرط حفظ boundaryها:

```text
UI design
Documentation
Test harness
Domain tests
```

این موارد قبل از validation مربوطه نباید lock شوند:

```text
Termux transport
PTY implementation
Persistence technology
Lifecycle strategy
Performance limits
```

---

# 6. Sprint Contract

هر Sprint باید قبل از شروع Card داشته باشد:

```text
Sprint ID
Goal
Why now
Dependencies
Inputs
Deliverables
Tests
Validation Gate
Risks
Out of Scope
Definition of Done
Exit Decision
```

---

# 7. Out-of-Scope Rule

هر Sprint باید صریحاً مشخص کند چه چیزهایی انجام نمی‌شود.

مثال:

```text
Sprint 13:
IN:
Project → Terminal → Action → Result

OUT:
Advanced session manager
Cloud sync
Plugin marketplace
AI automation
Theme engine
```

این قانون جلوی scope creep را می‌گیرد.

---

# 8. Sprint Exit Decisions

هر Sprint یکی از این نتایج را دارد:

```text
DONE
DONE WITH CONDITIONS
BLOCKED
REWORK
CANCELLED
DEFERRED
```

`DONE` بدون test/evidence معتبر نیست.

---

# 9. Risk Escalation

اگر issue دارای این ویژگی‌ها باشد:

```text
P0
cross-layer
data-loss potential
security-sensitive
lifecycle-critical
architecture-changing
```

باید از Sprint خارج شده و به Architecture Review برود.

---

# 10. WIP Limit

توصیه:

```text
1 major architecture-risk item
+
1 implementation stream
+
1 supporting stream
```

همزمانی زیاد باعث کاهش traceability می‌شود.

---

# 11. Definition of Done — Sprint

```text
[ ] Goal achieved
[ ] Deliverables exist
[ ] Tests pass
[ ] Critical failure path checked
[ ] No undocumented architecture bypass
[ ] Documentation updated
[ ] Open decisions recorded
[ ] Exit decision recorded
```

---

# 12. Definition of Done — Milestone

```text
[ ] All required Sprints complete
[ ] Critical gates passed
[ ] No unresolved P0
[ ] Evidence collected
[ ] Architecture remains coherent
[ ] E2E flow works
```

---

# 13. Milestones

```text
M0 — Reality Validated
    End of Sprint 6

M1 — Core Foundation
    End of Sprint 11

M2 — First Production Vertical Slice
    End of Sprint 16

M3 — Usable Atlas
    End of Sprint 20

M4 — Hardened Atlas
    End of Sprint 24

M5 — Release Candidate
    Sprint 25
```

این milestoneها feature-count نیستند؛ confidence boundaries هستند.

---

# 14. What We Deliberately Do Not Schedule Yet

تا وقتی evidence کافی وجود ندارد، زمان‌بندی قطعی برای این موارد داده نمی‌شود:

```text
Cloud sync
Plugin ecosystem
Advanced AI automation
Marketplace
Multi-device synchronization
Complex collaboration
```

این‌ها ممکن است بعداً ارزشمند باشند، اما نباید critical path فعلی را منحرف کنند.

---

# 15. Roadmap Governance

هر تغییر عمده در Roadmap باید مشخص کند:

```text
What changed?
Why?
Which dependency changed?
Which Sprint moved?
Which risk changed?
Does architecture change?
```

Architecture-changing changes require ADR review.

---

# 16. AI / Cursor Execution Rules

AI coding agent می‌تواند Sprint را اجرا کند فقط در محدوده:

```text
Goal
Deliverables
Contracts
Tests
Out-of-Scope
```

Agent نباید برای رسیدن به Sprint Done:

```text
architecture را عوض کند
test را حذف کند
error را مخفی کند
transport را عوض کند
scope را بی‌اجازه افزایش دهد
```

---

# 17. Recommended Working Loop

چرخه روزانه:

```text
Read current contract
 ↓
Check Sprint goal
 ↓
Select smallest task
 ↓
Implement
 ↓
Test
 ↓
Inspect diff
 ↓
Run architecture check
 ↓
Record decision/evidence
 ↓
Update Sprint status
```

---

# 18. First Actual Implementation Target

پس از عبور از H0:

```text
Create/Open Project
        ↓
Active Project
        ↓
Terminal
        ↓
Safe Action
        ↓
Termux
        ↓
Structured Result
        ↓
UI State
        ↓
Files
```

این مسیر باید قبل از featureهای فرعی پایدار شود.

---

# 19. Roadmap Success Metric

موفقیت Roadmap زمانی است که Atlas بتواند:

```text
execute reliably
+
explain failures
+
recover safely
+
preserve project identity
+
maintain explicit context
+
keep UI independent from transport
```

و همه این‌ها با evidence قابل تکرار همراه باشند.

---

# 20. Final Principle

> **Build the spine before the limbs.**

ستون فقرات Atlas:

```text
Action
Context
Project
Execution
Terminal
Filesystem
Result
Recovery
```

بعد از اینکه این ستون فقرات سالم شد، featureها می‌توانند روی آن رشد کنند.

---

## Document Status

**EXECUTION ROADMAP — ARCHITECTURE ALIGNED**

این سند مرجع ترتیب اجرای Atlas از Validation تا Release Candidate است.

**End of Document**
