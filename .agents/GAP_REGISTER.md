Purpose: Central repository of known technical debt, unvalidated formulas, missing interfaces, and architectural gaps requiring resolution.

# Technical Gap & Technical Debt Register

Every known architectural risk, unvalidated formula, or deferred technical item MUST be tracked in this register until fully resolved and verified.

---

## Registered Gaps & Risks

| ID | Gap / Risk Description | Severity | Affected Area | Status | Notes / Mitigation |
|---|---|---|---|---|---|
| **GAP-001** | Widget needs repository access outside the UI layer. | High | Jetpack Glance / Data Layer | Open | Jetpack Glance runs in a separate process context; requires Hilt entry points or WorkManager triggers to safely query Room without UI context. |
| **GAP-002** | Performance-score formula unvalidated on real gym training data. | Medium | Domain / Insight Engine | Open | Muscle Group Trend Score formula (% change vs baseline average) requires validation against edge cases (e.g., zero baseline, missing sessions). |
| **GAP-003** | Per-side dumbbell weight semantics ambiguity. | Medium | Data Model / Set Logger | Open | Exercises with `isPerSide = true` need clear UI indication whether logged `weightKg` is per single dumbbell or total combined weight. |
| **GAP-004** | No cloud sync or remote backup in V1. | Medium | Data Privacy / Portability | Open | Data is 100% local to device. User risk of data loss on phone damage/loss until SAF local CSV auto-backup (V3) is built. |
| **GAP-005** | `lb` unit conversion rounding inconsistencies. | Low | UI / Data Formatting | Open | Converting canonical `kg` to `lb` (`* 2.20462`) can produce floating point display issues if not rounded consistently across all steppers and screens. |
| **GAP-006** | Multi-gym tagging schema modeled in V1 DB schema. | Low | Data Model / Session Tags | Resolved | Optional `Session.gym` nullable text field added to V1 Room DB schema so Part 6 multi-gym tagging feature requires no migration. |
