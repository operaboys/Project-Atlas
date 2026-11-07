# Atlas Logger & Observability — Foundation and Maturity Charter

**Status:** Architecture direction accepted; Prototype and main-Atlas gates remain distinct.

## Decision

The Logger/Observability system developed and validated in Atlas Prototype is the foundational implementation line for the main Atlas Logger. It is not disposable prototype-only instrumentation. The validated core should be carried forward and evolved rather than discarded and rewritten without cause.

This decision applies specifically to the Logger foundation. It does not make the entire Prototype application, its UI scaffolding, adapters, database choices, or experiment-specific implementation canonical.

## What carries forward

- Canonical event envelope and typed payload contract
- Event taxonomy and truthful lifecycle semantics
- Producer boundaries and correlation model
- Sink abstractions and failure-isolation principles
- Validated reusable implementation and its tests
- Diagnostic coverage catalog and known platform limitations

The target implementation may be adapted to main Atlas architecture, but the semantic contracts and evidence should remain traceable.

## Prototype acceptance

Prototype work prioritizes broad, high-fidelity observability of real experimental paths: app/process lifecycle, foreground/background work, Action/Execution flow, Termux dispatch and callback, timeout/failure/recovery, diagnostic-system health, and correlation. Coverage should distinguish observed facts from inference and explicitly record Android limitations (for example, a force-stop may provide no callback).

Prototype success means useful diagnostic coverage and validated behavior. It does not claim production readiness.

## Main Atlas production gate

Before production release, the carried-forward foundation must be completed and hardened for:

- Privacy enforcement and secret/content protection
- Protected storage and access boundaries
- Retention, deletion and backup exclusion
- Export disclosure and access control
- Failure resilience, resource bounds and performance
- Release/device validation and explicit readiness evidence

## Non-negotiable constraints in both stages

- Never collect passwords, credentials, access tokens, private keys, passphrases or other secrets.
- Do not persist raw stdout/stderr, sensitive terminal input, private file content or raw exception messages as diagnostics.
- Broader diagnostic detail should be safe typed metadata; any special test-only behavior must use synthetic fixtures and stay out of production defaults.
- Existing Event Contract and Privacy Classification remain binding. Security hardening deferred to the main-Atlas gate does not waive their prohibitions.
- Do not infer that the current implementation emits every catalogued event. Source coverage and test evidence must be stated precisely.
- This charter authorizes no active database wiring, schema/bootstrap/migration change, workflow change, PR merge, or production rollout.

## Sources of truth

- Notion Decision Records: ADR-OBS-026
- Notion Logger & Observability Design Plan and Contracts
- Notion Engineering Handoff for current branch/commit/CI evidence
- This document and the canonical references below for product-level maturity

## Canonical references

- canonical/04_Termux_Integration.md — §63 Logging Policy
- canonical/06_Lite_Blueprint.md — §56 Logging
- canonical/10_Technical_Implementation.md — §§31–32 Observability / Logging Rules
