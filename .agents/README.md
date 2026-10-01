Purpose: Central index, mandatory read order, and Standing Agent Protocol for AI coding agents working on SpotOn.

# SpotOn Documentation Index

This directory contains standing rules, architectural constraints, and project context for SpotOn. Every future task in every future prompt MUST be executed according to these files.

## Documentation Files

| File | Description |
|---|---|
| `README.md` | Index, mandatory read order, and Standing Agent Protocol. |
| `PROJECT_CONTEXT.md` | Core mission, problem statement, target user profile, success criteria, and explicit non-goals. |
| `FEATURE_SCOPE.md` | Feature inventory, version mapping (V1/V2/V3), implementation status, and out-of-scope list. |
| `ARCHITECTURE.md` | Android tech stack, package structure, layer boundaries, MVVM + UDF patterns, and room persistence. |
| `AGENT_ARCHITECTURE.md` | Specification of the deterministic Insight Engine domain modules and calculation rules. |
| `DATA_MODEL.md` | Schema definitions, entities, fields, constraints, metric formulas, and archival invariants. |
| `ENGINEERING_RULES.md` | Hard rules for coding, package layout, UDF state, migrations, dependencies, and units. |
| `DEVELOPMENT_ORDER.md` | Sequential 10-part roadmap with goals, deliverables, exit criteria, and gating rules. |
| `CURRENT_STATE.md` | Living log of progress, current part, known issues, decisions, and added dependencies. |
| `GAP_REGISTER.md` | Table of known technical debt, unvalidated formulas, missing interfaces, and open architectural gaps. |
| `INTEGRATIONS.md` | Android system boundaries (Glance, WorkManager, DataStore, SAF, Notifications, Haptics, Keep-Awake). |
| `DATA_PRIVACY.md` | Security posture, local-only data storage, permissions policy, backup/export formats, and logging limits. |
| `TESTING.md` | Testing strategy, engine unit tests, DAO tests, UI tests, process death tests, and Definition of Done. |
| `UI_GUIDELINES.md` | Compose UX rules, dark theme, thumb-zone layout, tap targets, typography, haptics, and feedback. |
| `ACCEPTANCE_SCENARIOS.md` | Executable Given/When/Then acceptance test scenarios for validating features. |

---

## MANDATORY READ ORDER

Before undertaking ANY task on this codebase, an AI agent MUST read files in the following order:

1. `.agents/README.md` (This file - protocol & index)
2. `.agents/PROJECT_CONTEXT.md` (Mission, user principles, non-goals)
3. `.agents/CURRENT_STATE.md` (Current build progress & active decisions)
4. `.agents/ENGINEERING_RULES.md` (Hard coding rules & architecture constraints)
5. **Task-Specific Files** (Read relevant files based on task scope, e.g., `DATA_MODEL.md` for database work, `UI_GUIDELINES.md` for UI components, `DEVELOPMENT_ORDER.md` for roadmap planning).

---

## STANDING AGENT PROTOCOL

Before every task:
1. Read `README.md`, `PROJECT_CONTEXT.md`, `CURRENT_STATE.md`, `ENGINEERING_RULES.md`, and every file relevant to the task.
2. Confirm the task belongs to a feature listed in `FEATURE_SCOPE.md` and fits the current part in `DEVELOPMENT_ORDER.md`. If not, stop and ask.
3. Produce a short plan before writing code.

During every task:
4. Follow `ARCHITECTURE.md` and `AGENT_ARCHITECTURE.md`. Never put calculation logic in UI, ViewModels, or the widget.
5. Work in small, verifiable steps. Do not refactor or change unrelated code.
6. Do not add features, dependencies, or permissions that are not documented.
7. If a requirement is ambiguous or conflicts with these files, stop and ask instead of guessing.

After every task:
8. Write or update tests per `TESTING.md` and confirm they pass.
9. Update `CURRENT_STATE.md` (completed, in progress, next, decisions, dependencies).
10. Update `FEATURE_SCOPE.md` status and `GAP_REGISTER.md` if anything changed.
11. Tell the user how to run and verify the work.

If these files ever conflict with a user instruction, flag the conflict and ask before proceeding. If the user approves a change in direction, update the affected files in the same task so the documentation never goes stale.
