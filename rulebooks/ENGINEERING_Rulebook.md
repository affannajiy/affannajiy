# Engineering Rulebook

A pure reference: engineering principles reduced to *what the principle is* and a one-line
description. No app-specific architecture, implementation inventory, or history — those belong
in the project's own architecture/invariants document and changelog. Drop this file into any
project; it costs no tokens beyond what it takes to read the tables.

Three layers, answering different questions:

| | Section | Question it answers |
| --- | --- | --- |
| **§1** | Core engineering principles | *By design* — is this system simple, coherent, and maintainable? |
| **§2** | Code quality & reliability | *In implementation* — is the code correct, testable, and resilient? |
| **§3** | Delivery & maintenance | *Over time* — can the system evolve without accumulating uncontrolled risk? |

---

## §0 Goals these serve

| Goal | Succeeded when… |
| --- | --- |
| **Correctness** | The system does what it is supposed to do, including failure cases. |
| **Maintainability** | Another developer can understand and safely change it. |
| **Simplicity** | The solution contains no unnecessary machinery. |
| **Reliability** | Failures are contained, observable, and recoverable. |
| **Evolvability** | New requirements can be added without rewriting unrelated systems. |
| **Consistency** | Similar problems have similar solutions throughout the codebase. |

---

## §1 Core engineering principles

| # | Principle | What it demands |
| --- | --- | --- |
| 1 | **Single responsibility** | Each module, function, and component should have one coherent reason to change. |
| 2 | **Separation of concerns** | Keep presentation, business logic, data access, configuration, and infrastructure from becoming one tangled system. |
| 3 | **Single source of truth** | Store each piece of authoritative state in one place; derive everything else from it. |
| 4 | **Minimize coupling** | Components should depend on stable contracts rather than each other's internal implementation. |
| 5 | **Maximize cohesion** | Related logic belongs together; unrelated logic should not be bundled merely because it is convenient. |
| 6 | **Explicit dependencies** | A component should make its required dependencies visible rather than reaching for hidden global state. |
| 7 | **Prefer simplicity** | Choose the simplest solution that correctly satisfies the requirement. |
| 8 | **Avoid premature abstraction** | Do not create a generic abstraction until repeated structure or a real boundary justifies it. |
| 9 | **Don't repeat yourself** | Remove duplicated knowledge when doing so does not create a more complicated abstraction. |
| 10 | **You aren't gonna need it** | Do not build speculative features, extension points, or infrastructure without a current requirement. |
| 11 | **Stable contracts** | Interfaces between components should change less frequently than their implementations. |
| 12 | **Least astonishment** | Code should behave the way a reasonable developer would expect from its name, location, and surrounding conventions. |

---

## §2 Code quality & reliability

| # | Principle | What it demands |
| --- | --- | --- |
| 1 | **Correctness first** | A shorter or faster implementation is not better if it produces incorrect results. |
| 2 | **Fail explicitly** | Detect invalid states early and produce a deliberate failure rather than silently continuing with corrupted state. |
| 3 | **Handle errors at the right boundary** | Low-level code provides useful failure information; the layer that can recover decides how to present or handle it. |
| 4 | **Validate assumptions** | Preconditions, inputs, external responses, and important state transitions should be checked rather than assumed. |
| 5 | **Deterministic behaviour** | The same valid input and state should produce the same result unless nondeterminism is an intentional requirement. |
| 6 | **Test behaviour** | Tests should verify observable behaviour and important failure paths, not implementation details. |
| 7 | **Test the risk** | The most important tests cover the most consequential and failure-prone behaviour first. |
| 8 | **Keep functions focused** | A function should perform one understandable unit of work and avoid hidden side effects. |
| 9 | **Make state explicit** | Mutable state should be minimized, localized, and changed through controlled paths. |
| 10 | **Avoid hidden side effects** | Reading a value should not unexpectedly mutate state, trigger unrelated work, or alter global behaviour. |
| 11 | **Performance by evidence** | Optimize measured bottlenecks, not imagined ones. Correctness and clarity come first unless performance is itself a requirement. |
| 12 | **Resource discipline** | Files, connections, memory, timers, listeners, and other resources must have clear ownership and cleanup. |
| 13 | **Observable failures** | Important failures should be diagnosable through appropriate errors, logs, metrics, or user-visible state. |
| 14 | **No silent corruption** | Never hide invalid data, failed operations, or inconsistent state merely to keep execution moving. |

---

## §3 Delivery & maintenance

| # | Principle | What it demands |
| --- | --- | --- |
| 1 | **Small changes** | Prefer focused changes that solve one problem over broad rewrites. |
| 2 | **Preserve existing behaviour** | Do not change unrelated behaviour unless the change is explicitly required. |
| 3 | **Backward compatibility** | Existing interfaces, data, and workflows should remain functional unless a breaking change is deliberate. |
| 4 | **Dependency discipline** | Add a dependency only when its value justifies its maintenance, security, size, and complexity cost. |
| 5 | **Reversible changes** | Important changes should have a clear rollback or recovery path. |
| 6 | **Documentation at the boundary** | Document decisions, constraints, non-obvious behaviour, and public interfaces; do not document obvious syntax. |
| 7 | **Technical debt visibility** | Known compromises should be recorded rather than silently becoming permanent architecture. |
| 8 | **Keep the repository clean** | Remove dead code, obsolete assets, unused dependencies, temporary files, and abandoned experiments. |
| 9 | **Automate repetition** | Repeated validation, formatting, testing, building, and deployment tasks should be automated where practical. |
| 10 | **Verify before declaring done** | A change is not complete until the relevant tests, checks, builds, or manual verification have passed. |
| 11 | **Change for a reason** | Every meaningful architectural or behavioural change should have an identifiable reason. |
| 12 | **Leave the code better** | When touching code, improve obvious local problems where safe and directly relevant; do not turn every change into a rewrite. |

---

## §4 When principles conflict

| Conflict | Ruling |
| --- | --- |
| Simplicity wants less code; DRY wants abstraction | **Prefer duplication over a premature abstraction.** Repeated code is cheaper than a wrong abstraction. |
| Reusability wants generic components; YAGNI wants less code | **Build for demonstrated reuse.** Do not create extension points for hypothetical consumers. |
| Performance wants optimization; clarity wants simple code | **Measure first.** Optimize only where evidence shows the simpler implementation is insufficient. |
| Small changes want narrow scope; leave-the-code-better wants cleanup | **Contain the cleanup.** Fix directly related problems, but do not expand a feature change into an unrelated refactor. |
| Backward compatibility wants old behaviour; correctness requires change | **Preserve the contract where possible; break it deliberately when necessary.** Document the migration path. |
| Explicit dependencies increase setup; convenience wants globals | **Prefer explicit dependencies.** Hidden coupling is paid for later, during testing and maintenance. |
| Tests want implementation coverage; maintainability wants behaviour tests | **Test contracts and behaviour.** Implementation details should remain free to change. |
| Documentation wants detail; simplicity wants less | **Document decisions, not obvious code.** Explain why something exists when the code alone cannot. |

**Tiebreaker in every case:** favour the smallest change that preserves correctness, security,
usability, and future maintainability.

---

## How to use this document

1. Identify the engineering principles affected by the change.
2. Prefer existing project conventions over inventing a new pattern.
3. Make the smallest change that correctly solves the requirement.
4. Check failure paths, state changes, dependencies, and unintended side effects.
5. Verify the relevant tests, build, linting, or manual behaviour before declaring the change complete.
6. Keep this document pure reference. Project-specific architecture, current constraints, exceptions, and implementation decisions belong in the project's own architecture/invariants document; history belongs in the changelog.
7. When two principles conflict, §4 gives the ruling. Extend it only when the same conflict repeatedly appears.

This rulebook is a guide for engineering judgment, not a substitute for understanding the
project's actual requirements and architecture.
