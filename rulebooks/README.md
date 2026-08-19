# Rulebooks

Portable reference files. No project-specific content, no history — pure principle tables.
Drop any of these into a project (or hand to a person) and it works standalone.

Three lenses. Every significant change gets three questions:

```
                 ┌──────────────────┐
                 │      PROJECT     │
                 └────────┬─────────┘
                          │
             ┌────────────┼────────────┐
             ↓            ↓            ↓
          HUMAN         TRUST         CODE
             │            │            │
             ↓            ↓            ↓
          UI/UX       SECURITY    ENGINEERING
             │            │            │
             └────────────┼────────────┘
                          ↓
                   QUALITY DECISION
```

| Rulebook | Governs | Core question |
| --- | --- | --- |
| [UI-UX_Rulebook.md](UI-UX_Rulebook.md) | Human experience | Can someone understand and use this? |
| [SECURITY_Rulebook.md](SECURITY_Rulebook.md) | System & data | Can someone misuse, break, or exploit this? |
| [ENGINEERING_Rulebook.md](ENGINEERING_Rulebook.md) | Codebase | Can we change this without everything catching fire? |

Contents:

| File | Covers |
| --- | --- |
| UI-UX | Nielsen's 10 usability heuristics, 12 Gestalt principles, Laws of UX |
| Security | 7 principles of secure design, OWASP foundational principles, OWASP secure coding checklist, secure SDLC practices |
| Engineering | Core engineering principles, code quality & reliability, delivery & maintenance, conflict rulings |

## Where each thing lives

The rulebooks are **laws** — what good software generally does. They are not the only file
in the system:

| File | Answers |
| --- | --- |
| `CLAUDE.md` (project) | What does *this* software require, and how should the AI work on it? |
| Rulebooks | What should good software generally do? |
| `CHANGELOG.md` (project) | What happened? |

Keep AI behaviour instructions in `CLAUDE.md`, not in a rulebook — a rulebook about prompting
is meta-governance and does not govern the product. Keep project posture (what *this* app
actually does about each principle) in that project's own `SECURITY.md` / architecture /
invariants file. Never in here.

## How to use

Copy the file into the target repo (`docs/` or root), or point an agent at the raw URL:

```bash
curl -O https://raw.githubusercontent.com/affannajiy/affannajiy/main/rulebooks/ENGINEERING_Rulebook.md
```
