# Atlas — Technical Implementation Blueprint

> سند: `10_Technical_Implementation.md` 
> Version: **1.0 **
> Status: **Implementation Contract — Architecture-Derived** 
> Date: 2026-08-31 
> Depends on: `01_Core_Philosophy.md`, `02_Capability_Model.md`, `03_Action_Architecture.md`, `04_Termux_Integration.md`, `05_Project_Workspace.md`, `06_Lite_Blueprint.md`, `07_UI_Component_Blueprint.md`, `08_Design_System.md`, `09_Validation_Gates.md`

---

##1. Purpose

این سند معماری Atlas را به یک blueprint اجرایی تبدیل می‌کند؛ بدون اینکه unresolved decisions را زودتر از موعد lock کند.

هدف:

Validated Architecture
        ↓
Implementation Boundaries
        ↓
Modules
        ↓
Contracts
        ↓
State / Data Flow
        ↓
Build Order
        ↓
Test Strategy

اصل:

> **Implementation must express the architecture, not silently redefine it.**

##2. Implementation Status Model

هر جزء implementation باید یکی از وضعیت‌های زیر را داشته باشد:

LOCKED
    تصمیم معماری معتبر و قابل اجرا

CONDITIONAL
    فقط تحت شرط مشخص

PROTOTYPE
    برای validation؛ هنوز production contract نیست

OPEN
    تصمیم هنوز باز است

DEFERRED
    عمداً به آینده منتقل شده

قانون:

> **OPEN ≠ permission to guess**

##3. Target Architecture

مدل اجرایی:
┌──────────────────────────────┐
│           Android UI        │
│ Screens / Components / UX   │
└──────────────┬───────────────┘
               │ Intent
               ▼
┌──────────────────────────────┐
│       Presentation Layer     │
│ View State / Reducer / VM    │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│          Domain Core         │
│ Actions / Context / Rules    │
│ Validation / Result Model    │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        Port / Abstraction    │
│ Execution / Files / Project  │
│ Terminal / Persistence       │
└──────────────┬───────────────┘
               │
        ┌──────┴───────┐
        ▼              ▼
┌──────────────┐ ┌──────────────┐
│ Termux       │ │ Android      │
│ Adapter      │ │ Platform     │
└──────────────┘ └──────────────┘
وابستگی باید از بالا به abstraction و از adapter به external system باشد.

##4. Recommended Technology Baseline

فناوری‌ها تا زمانی که prototype آن‌ها را validate نکرده، نباید به‌عنوان حقیقت معماری تلقی شوند.

Baseline پیشنهادی:

Language: Kotlin
UI: Jetpack Compose
Architecture: Unidirectional State Flow
Async: Kotlin Coroutines / Flow
DI: constructor-first; framework optional
Persistence: repository abstraction
Testing: unit + integration + device tests
Build: Gradle

موارد زیر هنوز evidence-dependent هستند:

Exact minSdk
Exact database
Exact PTY implementation
Exact Termux transport
Exact process/session implementation

##5. Module Strategy

شروع پروژه نباید با تعداد زیادی Gradle module پیچیده شود.

Baseline:

:app
:core:model
:core:domain
:core:execution
:core:filesystem
:core:project
:core:terminal
:core:persistence
:feature:home
:feature:project
:feature:files
:feature:terminal

اما split فیزیکی moduleها باید بر اساس dependency boundary واقعی انجام شود؛ package مرزبندی کافی نیست اگر dependencyها آن را نقض کنند.

##6. Layer Responsibilities

UI

مسئول:
render state
collect user intent
display feedback
navigation
accessibility

غیرمسئول:
shell command
filesystem mutation
Termux transport
business rules
Presentation

مسئول:
map domain state → UI state
receive intents
dispatch actions
manage screen lifecycle

غیرمسئول:
raw external-system calls
Domain

مسئول:
Action
Capability
Context
Validation
Result
Business invariants

باید تا حد امکان Android-independent باشد.

Adapter

مسئول:
translate domain request
to external-system operation
Infrastructure

مسئول:
Termux
filesystem
Android APIs
persistence
process/terminal infrastructure

##7. Core Domain Contracts

7.1 Action

Conceptual contract:
interface Action<I, O> {
    val id: ActionId
    suspend fun execute(input: I, context: ExecutionContext): ActionResult<O>
}
Implementation may differ, but these concepts must survive:
identity
input
context
result

7.2 ExecutionContext
Context should be explicit.

Conceptual fields:
projectId
projectRoot
currentLocation
environment
capabilities
sessionId
Do not hide critical context inside globals.

7.3 ActionResult

Conceptual model:
Success(value)
Failure(error)
Cancelled
Unknown
Optional:
Running
Progress
Warning
PartialSuccess

The final model must reflect real execution semantics validated by prototypes.

##8. Capability Model

Capabilities are not merely UI labels.

Conceptually:
CapabilityId
CapabilityState
CapabilityRequirements
CapabilityAvailability

Examples:
FileRead
FileWrite
CommandExecution
Terminal
ProjectManagement
Network

Capability checks should happen before attempting an operation when practical.

##9. Intent Model

UI emits semantic intent:
OpenProject
CreateProject
NavigateTo
RunAction
OpenTerminal
CreateFile
DeleteFile
Retry
Cancel

Avoid:
OnClickRunCommand("...")
The UI should express user intent, not transport details.

##10. Presentation State

Each feature should have explicit state.

Example:
data class FeatureState(
    val loading: Boolean,
    val content: ContentState,
    val error: UiError?,
    val operation: OperationState?
)
Do not represent meaningful states through scattered booleans.
Prefer explicit state machines when states are mutually exclusive.

##11. Project Model

Project identity must be stable.

Conceptual:
Project
 ├── id
 ├── name
 ├── rootReference
 ├── metadata
 └── settings

Important:
Project.id ≠ root path
A missing root must be representable.

##12. Filesystem Model

Filesystem operations should be represented through an abstraction.

Conceptual:
FileSystem
 ├── list
 ├── read
 ├── write
 ├── create
 ├── move
 ├── copy
 ├── rename
 └── delete

Mutation methods must return explicit results.
Conflicts and permission failures must not disappear inside generic exceptions.

##13. Terminal Model

Terminal is a first-class subsystem.

Conceptually:
TerminalSession
 ├── sessionId
 ├── state
 ├── input channel
 ├── output stream
 ├── error stream
 └── lifecycle

Possible states:
Created
Starting
Running
Stopping
Exited
Failed
Unknown

Exact state model is subject to prototype evidence.

##14. Terminal Boundary

UI must never depend directly on PTY or Termux details.
Required boundary:

Terminal UI
 ↓
Terminal Controller / Domain API
 ↓
Terminal Session abstraction
 ↓
Terminal Adapter
 ↓
External terminal/process system

This boundary exists to keep transport replaceable.

##15. Persistence Boundary

Do not expose persistence technology to domain code.

Domain
 ↓
Repository
 ↓
Data source
 ↓
Storage

Repository contracts should express domain needs, not SQL/ORM details.

Example:
ProjectRepository
SessionRepository
SettingsRepository

Exact storage remains OPEN until requirements and prototype evidence justify a choice.

##16. Navigation

Navigation should be a presentation concern.

Conceptual:
User Intent
 ↓
Navigation Decision
 ↓
Destination

Do not make domain code know about:
NavController
Composable route strings
Android Activity

##17. Error Architecture

Errors should have at least three levels:
Domain Error
     ↓
Presentation Error
     ↓
User-facing Message

Example:
PermissionDenied
    ↓
CannotWriteResource
    ↓
"Atlas can't write to this location."

Technical detail should remain inspectable where useful.

##18. Cancellation

Cancellation must be designed, not inferred from UI disappearance.

Every long-running operation should answer:
Can it be cancelled?
What happens to external process?
What result is returned?
Can cancellation race with success?

Do not automatically equate:
UI disposed = operation cancelled

##19. Unknown Outcome

This is a first-class implementation requirement.

Example:
Request sent
 ↓
Connection lost
 ↓
External result unknown

Correct:
Unknown

Incorrect:
Success
or:
Failure

unless evidence supports it.

##20. Side-Effect Classification

Actions should declare or otherwise make clear their side-effect characteristics.

Conceptually:
READ-ONLY
REVERSIBLE
MUTATING
DESTRUCTIVE

This can drive confirmation and retry policy.

##21. Retry Policy

Retry should be action-aware.
Safe retry
Unsafe retry
Conditional retry
No retry

Never implement:
catch → retry automatically
for unknown side effects.

##22. Concurrency

Critical operations need explicit concurrency semantics.

Questions:
Can two instances run?
Can same project mutate concurrently?
Can two writes target same resource?
Who owns cancellation?
Which result wins?

Do not solve concurrency by arbitrary global locks before evidence.

##23. State Ownership

One source of truth per meaningful state.

Example:
Project selection → Project feature/domain
Terminal session → Terminal subsystem
UI presentation state → Presentation layer

Avoid duplicate state such as:
Project root in UI
Project root in VM
Project root in repository

without a defined synchronization contract.

##24. Dependency Rules

Allowed:
UI → Presentation
Presentation → Domain
Domain → Abstraction
Adapter → Abstraction
Adapter → External system

Disallowed:
UI → Adapter
UI → Termux
Domain → Compose
Domain → Android Activity
Domain → concrete database
Feature A → Feature B internals

Exceptions require an architecture decision record.

##25. Package Structure

Illustrative:
atlas/
  app/

  core/
    model/
    domain/
    execution/
    filesystem/
    project/
    terminal/
    persistence/

  feature/
    home/
    project/
    files/
    terminal/
    settings/

  platform/
    termux/
    android/

Package names are implementation details; dependency direction is not.

##26. Testing Pyramid

Required levels:
Unit Tests
   ↓
Domain / Contract Tests
   ↓
Integration Tests
   ↓
Device Tests
   ↓
End-to-End Critical Flows

Not every component needs the same test depth.

##27. Domain Tests

High priority:
Action validation
Context rules
Result mapping
Capability checks
Project invariants
Retry policy
Side-effect classification

These should run fast and without Android.

##28. Adapter Tests

Adapters require:
happy path
external failure
malformed response
timeout
cancellation
unknown outcome
resource cleanup

Use fakes/mocks where appropriate, but retain real device integration tests for system boundaries.

##29. Device Tests

Required for:
Termux integration
terminal
filesystem permissions
Android lifecycle
keyboard/IME
background behavior
real rendering

A desktop unit test cannot substitute for these.

##30. End-to-End Critical Flow

Minimum:
Launch
 ↓
Open/Create Project
 ↓
Set active context
 ↓
Open Terminal
 ↓
Execute Action
 ↓
Observe result
 ↓
Navigate Files
 ↓
Mutate resource
 ↓
Trigger failure
 ↓
Recover

This flow should become a release-gate test.

##31. Observability

Atlas should make important execution behavior diagnosable.

Conceptual events:
IntentReceived
ActionStarted
ActionValidated
ActionSucceeded
ActionFailed
ActionCancelled
ActionUnknown
SessionStarted
SessionEnded
ProjectOpened
ProjectRootMissing

Logs must not leak sensitive data unnecessarily.

##32. Logging Rules

Logs should answer:
What happened?
When?
Which subsystem?
Which operation?
What was the outcome?

Avoid logging:
raw secrets
credentials
unnecessary user data

##33. Build Configuration

Separate:
debug
release
test

from product logic.
Build-time configuration should not become hidden runtime state.

##34. Feature Flags

Use feature flags only when they serve a real validation/release need.

Avoid permanent:
if (experimental)
branches without ownership and removal criteria.

Every temporary flag needs:
purpose
owner
created date
removal condition

##35. Migration Strategy

If contracts change:
Old contract
 ↓
Compatibility layer / migration
 ↓
New contract

Do not silently mutate persisted data without a migration strategy.

##36. Security Baseline

At minimum:
Least privilege
Explicit destructive actions
No embedded secrets
Safe logging
Input validation
Path validation
Process argument safety

Never construct shell commands by unsafe string concatenation when structured arguments are possible.

##37. Destructive Operations

Destructive actions must provide:
clear intent
scope
confirmation when appropriate
result
recovery/undo when feasible

A generic delete() call is not enough to define UX safety.

##38. Resource Lifecycle

Every external resource must have an owner.

Examples:
Terminal session → Terminal subsystem
Process → execution/session owner
File handle → filesystem operation
Subscription → lifecycle owner

Every owner must define cleanup.

##39. Memory / Streaming

Large output must not automatically become:
entire process output → one giant String → UI

Streaming/backpressure strategy should be used where evidence requires it.
Exact buffer sizes are benchmark decisions, not architecture principles.

##40. Performance Implementation Rules

Optimize after measurement.

Preferred:
measure
 ↓
identify bottleneck
 ↓
optimize
 ↓
re-measure

Avoid premature:
caching everywhere
global singleton state
custom renderer
custom storage
without evidence.

##41. Build Order

Recommended implementation sequence:
Phase 1 — Skeleton
Phase 2 — Core Models
Phase 3 — Action Engine
Phase 4 — Project Model
Phase 5 — Filesystem Adapter
Phase 6 — Termux Adapter
Phase 7 — Terminal Core
Phase 8 — First Vertical Slice
Phase 9 — Error / Lifecycle
Phase 10 — UI Expansion
Phase 11 — Performance
Phase 12 — Hardening

##42. Phase 1 — Skeleton

Deliver:
Gradle setup
package structure
dependency rules
basic app shell
test infrastructure
No feature sprawl.

##43. Phase 2 — Core Models

Implement:
IDs
Project
Context
Action
Result
Error
Capability
Only stable contracts.

##44. Phase 3 — Action Engine

Implement:
Intent dispatch
Action validation
Execution context
Result mapping
Cancellation semantics

Then test end-to-end with a fake adapter.

##45. Phase 4 — Project Model

Implement:
ProjectRepository
Project identity
Root reference
Active project context
Missing root state

Do not overbuild project metadata.

##46. Phase 5 — Filesystem Adapter

Implement:
list
read
write
create
rename
move
copy
delete

with explicit error mapping.

##47. Phase 6 — Termux Adapter

Only after Termux Reality Gate evidence.
Implement the selected transport behind the adapter.
Do not leak transport types upward.

##48. Phase 7 — Terminal Core

Implement only the capabilities validated by prototype:
session lifecycle
input
output
error
close
state recovery

Avoid premature advanced terminal features.

##49. Phase 8 — First Vertical Slice

Build one real path:
Project
 ↓
Terminal
 ↓
Action
 ↓
Termux
 ↓
Result
 ↓
Files

This is the first architecture proof inside production code.

##50. Phase 9 — Error / Lifecycle

Add:
failure states
retry
cancel
unknown
background/foreground
recovery

before broadening feature count.

##51. Phase 10 — UI Expansion

Only after vertical slice stability:
Home
Projects
Files
Terminal
Settings
Expand progressively.

##52. Phase 11 — Performance

Use actual benchmark results.
No arbitrary micro-optimization.

##53. Phase 12 — Hardening

Focus:
edge cases
permissions
lifecycle
recovery
accessibility
security
migration
release behavior

##54. Definition of Done — Component

A component is done when:
[ ] Contract exists
[ ] Dependency direction is valid
[ ] Main behavior implemented
[ ] Failure behavior implemented
[ ] Tests exist
[ ] Lifecycle/resource ownership is clear
[ ] Logging is appropriate
[ ] No unresolved P0

##55. Definition of Done — Feature

[ ] User intent defined
[ ] UI state defined
[ ] Domain Action defined
[ ] Capability checked
[ ] Errors mapped
[ ] Recovery defined
[ ] Critical accessibility checked
[ ] Device behavior verified where needed
[ ] Architecture audit clean

##56. Definition of Done — Release

[ ] Critical E2E flow passes
[ ] P0/P1 issues resolved or explicitly accepted
[ ] No architecture bypass
[ ] Lifecycle verified
[ ] Termux integration verified
[ ] Performance baseline acceptable
[ ] Accessibility baseline acceptable
[ ] Release configuration verified

##57. ADR Trigger

An Architecture Decision Record is required when implementation introduces:
new external dependency
new cross-layer dependency
new persistence technology
new transport
new lifecycle strategy
new security-sensitive behavior
new state ownership rule
exception to dependency direction

##58. Change Control

Implementation must not silently modify:
Action semantics
Project identity
Context semantics
Capability meaning
Result states
Adapter boundary

without updating the corresponding architecture decision.

##59. Cursor / AI Coding Contract

AI coding agents may:
implement explicit contracts
write tests
refactor within boundaries
generate repetitive code

They may not autonomously:
change architecture
introduce new transport
change project identity
bypass adapters
remove tests to make build green
replace errors with generic success/failure

Any architecture-impacting change becomes a review item.

##60. AI Guardrail

Required loop:
Read relevant contract
 ↓
Plan
 ↓
Implement smallest coherent change
 ↓
Run tests
 ↓
Inspect diff
 ↓
Check architecture boundary
 ↓
Report unresolved assumptions

The agent must not infer:
missing requirement → arbitrary implementation

##61. Implementation Checklist

Before coding:
[ ] Relevant architecture document read
[ ] Validation status known
[ ] Contract identified
[ ] Dependencies known
[ ] Failure cases identified
[ ] Test strategy defined

During coding:
[ ] Boundary preserved
[ ] No hidden state
[ ] No raw transport leakage
[ ] Tests updated
[ ] Errors explicit

After coding:
[ ] Build passes
[ ] Tests pass
[ ] Diff reviewed
[ ] Architecture audit performed
[ ] Documentation updated
[ ] Decision log updated if needed

##62. Technical Debt Rule

Technical debt is acceptable only when:
intentional
documented
bounded
owned
reviewable
Not acceptable:

temporary workaround with no expiry

##63. Open Decisions Register

At the time of this blueprint, the following remain evidence-dependent:
Termux transport       OPEN / validation-dependent
PTY mechanism          OPEN / validation-dependent
Persistence technology OPEN / requirements-dependent
Exact minSdk           OPEN
Session limits         OPEN
Memory budgets         OPEN
APK size target        OPEN
Background strategy    OPEN
Terminal history       OPEN
Visual token values    OPEN

These must not be invented merely to complete a code skeleton.

##64. First Production Vertical Slice

The first serious implementation target should be:
Create/Open Project
        ↓
Set Active Project
        ↓
Open Terminal
        ↓
Execute one safe Action
        ↓
Receive structured Result
        ↓
Reflect Result in UI
        ↓
Return to Files
        ↓
Verify resource state

It should demonstrate the complete architecture, not the maximum number of features.

##65. Architecture Exit Criteria

Technical implementation can scale only after:
Action boundary works
Project identity works
Termux boundary works
Terminal lifecycle works
Failure model works
Unknown outcome works
UI/domain separation holds
Critical E2E flow works

##66. Final Principle

The first implementation must prove the architecture, not merely produce screens.
Atlas should optimize for:
clarity
correct boundaries
observable behavior
recoverability
evidence

before optimizing for feature count.

##67. Next Step

بعد از این سند، پروژه وارد مرحله‌ای می‌شود که باید implementation را به Roadmap + Sprint Breakdown تبدیل کرد.

آن Roadmap نباید صرفاً فهرست featureها باشد. هر Sprint باید:
Goal
Dependencies
Deliverables
Tests
Validation gates
Risks
Definition of Done
داشته باشد.

---

##Document Status

**IMPLEMENTATION CONTRACT — ARCHITECTURE-DERIVED**

این سند مرجع فنی برای تبدیل architecture تاییدشده Atlas به implementation است.

**End of Document**