Purpose: The ordered 10-part build plan detailing goals, deliverables, exit criteria, and strict progression gating rules.

# Development Roadmap & Order

Build order in DEVELOPMENT_ORDER.md always takes precedence over the version labels (V1/V2/V3), which only indicate product priority.

Progress through these parts sequentially. **STRICT RULE**: Never start a part before all exit criteria of the preceding part are fully met and verified by automated tests and manual emulator testing.

---

## Part 1: Project Foundation & Database Schema
- **Goals**: Initialize Android project, Hilt dependency injection, Room database, DataStore, and Navigation Compose shell.
- **Deliverables**:
  - `SpotOnApplication` with `@HiltAndroidApp`.
  - Room DB with the full v1 schema: MuscleGroup, Exercise, Session (id, exerciseId, date, tags, note, gym nullable text, isCompleted), SetEntry (id, sessionId, setIndex, weightKg, reps, type [normal|warmup|drop|failure], rir nullable, createdAt), and ChangeHistory. BodyWeightEntry may be added in Part 7 via migration.
  - Pre-seeded standard muscle groups (Chest, Back, Shoulders, Biceps, Triceps, Forearms, Legs, Abs).
  - DataStore settings repository.
  - Bottom navigation / Navigation Host shell.
- **Exit Criteria**: App builds cleanly, Room database initializes with seed data, navigation shell renders dark theme UI without errors.

---

## Part 2: Muscle Group & Exercise Catalog
- **Goals**: Full catalog management for Muscle Groups and Exercises.
- **Deliverables**:
  - Muscle Group list & detail screen.
  - Exercise list screen per muscle group.
  - Create / Edit exercise screen (equipment type, target rep range, notes).
  - Create and rename custom muscle groups.
  - Custom sort ordering mechanism.
  - Soft-archiving logic for muscle groups and exercises.
- **Exit Criteria**: User can create, rename, add, edit, reorder, and archive muscle groups and exercises. Soft-archived items vanish from active lists but persist in DB.

---

## Part 3: Core Set Logger (V1 Engine Baseline)
- **Goals**: Implement fast, resume-safe set logging with same-weight feedback.
- **Deliverables**:
  - `SetComparisonEngine` implementation.
  - Active session logging screen with large custom numeric keypad and weight steppers (+/- 2.5kg).
  - Pre-fill weight logic from prior set.
  - One-tap "Same as last time" action button.
  - Warm-up set toggle.
  - Instant set persistence to Room.
  - Immediate same-weight set comparison feedback (+N, -N, same reps).
  - Undo set feature.
  - Keep-screen-awake integration while on logger screen.
- **Exit Criteria**: User can log a set in ≤ 2s. Process death mid-session restores exact state upon relaunch.

---

## Part 4: Session Summary, Rep PRs & Progression Prompts
- **Goals**: Enhance logger with PR detection and double-progression suggestions.
- **Deliverables**:
  - `RepPrEngine` implementation.
  - `DoubleProgressionEngine` implementation.
  - Session completion summary screen.
  - Rep PR indicator on the set row.
  - Double-progression suggestion prompt when top rep target is hit on all working sets.
- **Exit Criteria**: Rep PRs display indicators accurately; double-progression prompts display reliably when criteria are met.

---

## Part 5: Exercise Graphs & Muscle Group Dashboard
- **Goals**: Build progress visualizers and high-level muscle group analytics.
- **Deliverables**:
  - `MuscleTrendEngine` and `HardSetVolumeEngine` implementations.
  - Exercise history graph (top weight & total working reps over time, and best reps per weight, never weight x reps volume).
  - Muscle Group Dashboard (trend score % vs baseline, hard set counts per week, days since last trained).
- **Exit Criteria**: Graphs render accurately offline; trend score correctly computes average % change per exercise without raw kg summation.

---

## Part 6: Advanced Editing, Back-Dating, Merging & Set Types (V2)
- **Goals**: Support deep editing capabilities, session back-dating, and exercise consolidation.
- **Deliverables**:
  - Historical set & session editor.
  - Back-dating session calendar picker.
  - Exercise duplicate merger tool (re-pointing historical sessions while recording to `ChangeHistory`).
  - Set types: Normal, Warmup, Drop, Failure.
  - RIR (Reps in Reserve) optional logging.
  - Session tags and session-level notes.
  - Multi-gym tag: an optional gym/location field on sessions, selectable and filterable.
- **Exit Criteria**: Back-dated sets update historical metrics correctly; exercise merge preserves all set history without orphaned records.

---

## Part 7: Advanced Analytics & Body Weight (V2/V3)
- **Goals**: Implement plateau detection, push/pull balance, weekly recaps, and body weight tracking.
- **Deliverables**:
  - `PlateauDiagnosticEngine` implementation.
  - `PushPullBalanceEngine` implementation.
  - `WeeklyRecapEngine` implementation.
  - Body weight logging screen & timeline graph.
  - Consistency activity heatmap view.
- **Exit Criteria**: 4-session stagnation triggers plateau flag; push/pull ratio renders correctly; body weight logs render on timeline.

---

## Part 8: Home-Screen Widget Quick-Log (V3)
- **Goals**: Enable rapid set logging directly from the Android launcher.
- **Deliverables**:
  - Jetpack Glance Home-Screen Widget.
  - Widget repository interface & background updater.
  - Quick-log dialog/activity trigger from widget button.
- **Exit Criteria**: The user taps the widget, enters reps in a quick-log dialog without opening the main app, and the set is saved and visible in the main app.

---

## Part 9: Auto-Backup & Data Portability (V3)
- **Goals**: Offline data security, local backups, and CSV export/import.
- **Deliverables**:
  - Storage Access Framework (SAF) integration.
  - Automated local database backup via WorkManager.
  - Full CSV export and import engine.
  - Backup restore validator and corruption handler.
- **Exit Criteria**: Exported CSV opens cleanly in spreadsheets; restoring backup reinstates 100% database state cleanly.

---

## Part 10: Final Polish, Optimization & Compliance Audit
- **Goals**: Edge-case handling, UI polish, performance optimization, and audit against all `.agents/` rules.
- **Deliverables**:
  - Accessibility audit (tap targets ≥ 56dp, contrast, content descriptions).
  - Performance profiling (Compose recomposition optimization, smooth scrolling).
  - Full test suite execution (100% engine test coverage).
- **Exit Criteria**: All unit, DAO, ViewModel, and UI tests pass; zero memory leaks; full compliance with `ENGINEERING_RULES.md`.
