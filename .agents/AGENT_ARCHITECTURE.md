Purpose: Specification of the deterministic, pure-domain Insight Engine architecture and its calculation modules.

# Insight Engine Architecture

The **Insight Engine** is the deterministic calculation core of SpotOn. It processes historical set entries, sessions, and exercises to compute real-time performance feedback, progression prompts, and muscle group analytics.

---

## 1. Engine Core Constraints

1. **Pure Kotlin**: Zero imports from `android.*`, `androidx.*`, or UI libraries.
2. **Determinism**: Given identical input entities, every engine function MUST return the exact same calculation result every time.
3. **No I/O or Side Effects**: Engine modules do not read databases, write preferences, or trigger network calls. They receive domain models and return calculated value objects.
4. **Zero UI Leaks**: No Composable, ViewModel, DAO, or Glance Widget may implement metric formulas. All logic must reside within `com.spoton.domain.engine.*`.

---

## 2. Engine Modules

```
com.spoton.domain.engine/
├── SetComparisonEngine.kt       # Same-weight comparison vs prior session
├── RepPrEngine.kt               # Best reps achieved at target weight
├── DoubleProgressionEngine.kt   # Weight addition recommendation prompts
├── MuscleTrendEngine.kt         # Muscle group trend score calculation (% change vs baseline)
├── HardSetVolumeEngine.kt       # Working rep & hard set counters (warm-ups excluded)
├── PlateauDiagnosticEngine.kt   # 4-session plateau detection
├── PushPullBalanceEngine.kt     # Push vs Pull hard set / working rep ratio evaluation
└── WeeklyRecapEngine.kt         # Aggregated weekly metrics builder
```

---

## 3. Module Specifications

### A. SetComparisonEngine
- **Inputs**: Current `SetEntry`, list of `SetEntry` items from prior completed `Session` for the same exercise.
- **Rule**: Compare current set reps ONLY against a prior set at the **EXACT SAME WEIGHT (`weightKg`)**, regardless of set index/position.
- **Outputs**:
  - `DELTA_REPS(diff: Int)`: e.g., +2 reps, -1 rep, 0 reps at weight X.
  - `NO_MATCH`: No prior set recorded at weight X in the previous session.

### B. RepPrEngine
- **Inputs**: Current `SetEntry`, entire historical list of `SetEntry` for the exercise.
- **Rule**: A Personal Record (PR) is flagged IF `type != WARMUP` AND reps > maximum reps previously completed at that specific `weightKg`.
- **Outputs**: `IS_REP_PR(weightKg, reps, previousBestReps)` boolean flag.

### C. DoubleProgressionEngine
- **Inputs**: Target rep range (`targetRepMin`, `targetRepMax`), all working sets (`type == NORMAL | FAILURE`) of the current session.
- **Rule**: Recommend weight increase IF 100% of working sets reach or exceed `targetRepMax`.
- **Outputs**: `ProgressionRecommendation(shouldIncreaseWeight: Boolean, suggestedAddKg: Double)`.

### D. MuscleTrendEngine
- **Inputs**: List of exercises for a `MuscleGroup`, historical sessions over evaluation window (e.g., 30 days).
- **Rule**: Compute the percentage change of each exercise against its own baseline (average performance over prior 4 sessions), then calculate the average of these exercise percentage changes.
- **CRITICAL INVARIANT**: NEVER sum raw kg lifted across different exercises.

### E. HardSetVolumeEngine
- **Inputs**: List of `SetEntry` items for a target period (e.g., trailing 7 days).
- **Rule**: Warm-up sets (`type == WARMUP`) are **STRICTLY EXCLUDED** from all hard-set and working-rep totals. Hard sets count working sets (`NORMAL`, `DROP`, `FAILURE`) with RIR ≤ 3 or target reps met. Exercise performance graphs visualize top weight and total working reps over time (and best reps per weight), never weight x reps volume.
- **Outputs**: `HardSetCount`, `WorkingRepCount`.

### F. PlateauDiagnosticEngine
- **Inputs**: Last 4 completed `Session` entries and associated `SetEntry` data for an exercise.
- **Rule**: Flag plateau IF no weight or rep progression is achieved across 4 consecutive sessions.
- **Outputs**: `IsPlateaued: Boolean`, `PlateauSessionCount: Int`.

### G. PushPullBalanceEngine (V2)
- **Inputs**: Completed sessions across Push muscle groups (Chest, Shoulders, Triceps) vs Pull muscle groups (Back, Biceps, Forearms). (Legs and Abs are tracked separately).
- **Outputs**: Ratio of hard sets and working reps (Push vs Pull) over rolling 7/14/30 day periods.

### H. WeeklyRecapEngine (V3)
- **Inputs**: Completed sessions, sets, and body weight entries within a calendar week.
- **Outputs**: `WeeklyRecapSummary` (total hard sets per muscle group, exercise PR count, body weight average delta).
