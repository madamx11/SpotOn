Purpose: Comprehensive testing strategy, test suite organization, coverage requirements, and Definition of Done for all development tasks.

# Testing Strategy & Definition of Done

No task is considered complete until all unit tests, DAO tests, ViewModel tests, UI tests, and manual emulator checks are executed and passed.

---

## 1. Test Suite Categories & Requirements

```
                                 [ UI & Compose Tests ]
                                 (Logger, Keypad, Nav)
                                           |
                                [ ViewModel State Tests ]
                                (StateFlow, Intent Flow)
                                           |
                    +----------------------+----------------------+
                    |                                             |
        [ DAO & Room DB Tests ]                      [ Insight Engine Tests ]
       (In-Memory SQLite, Queries)                 (100% Deterministic Unit)
```

### A. Domain Insight Engine Unit Tests (100% Coverage Target)
- **Location**: `src/test/java/com/spoton/domain/engine/`
- **Framework**: JUnit 5 / Kotlin Test + Truth assertions.
- **Mandatory Test Cases**:
  - `SetComparisonEngineTest`: Validates same-weight rep comparison (+N, -N, 0) against previous session set at identical weight, regardless of set index.
  - `RepPrEngineTest`: Validates PR flag emission only when reps exceed prior maximum at target weight (ignoring warm-ups).
  - `DoubleProgressionEngineTest`: Verifies weight addition prompts when 100% of working sets hit `targetRepMax`.
  - `MuscleTrendEngineTest`: Verifies trend score calculation (% change vs baseline average) across exercises without raw kg summation.
  - `HardSetVolumeEngineTest`: Ensures warm-up sets (`WARMUP`) are strictly excluded from all volume/count calculations.
  - `PlateauDiagnosticEngineTest`: Verifies 4-session stagnation triggers plateau flag.
  - `PushPullBalanceEngineTest`: Validates push vs pull volume ratio computations.
  - `WeeklyRecapEngineTest`: Validates weekly summary aggregation.

### B. DAO & Room Database Tests
- **Location**: `src/androidTest/java/com/spoton/data/local/dao/`
- **Framework**: AndroidJUnit4 + Room In-Memory Database.
- **Mandatory Test Cases**:
  - `SessionDaoTest`: Verifies last-session retrieval query for a specific exercise.
  - `ExerciseDaoTest`: Verifies soft-archiving filtering (`isArchived = false` in active queries).
  - `MergeExercisesTest`: Verifies merging duplicate exercises re-points historical `sessions` records atomically while preserving all `set_entries`.

### C. ViewModel Unit Tests
- **Location**: `src/test/java/com/spoton/ui/`
- **Framework**: JUnit 4 + KotlinX Coroutines Test (`MainDispatcherRule`) + Turbine for `StateFlow` testing.
- **Mandatory Test Cases**:
  - Validates user intents emit correct single `StateFlow` UI state transitions.
  - Validates pre-fill weight logic from previous set upon logger screen initialization.

### D. Compose UI & Logger Tests
- **Location**: `src/androidTest/java/com/spoton/ui/`
- **Framework**: Compose UI Test (`createComposeRule()`).
- **Mandatory Test Cases**:
  - Keypad interaction & stepper adjustment tests.
  - One-handed logging flow (Numeric input -> Tap checkmark -> Immediate set row render).
  - Touch target size verification (Minimum 56dp for all logger buttons).

### E. Process Death & Resume-Safety Tests
- **Location**: `src/androidTest/java/com/spoton/ui/feature/logger/`
- **Test Flow**: Simulate mid-session app termination via `StateKeeper` / Activity recreation. Verify reopening app restores exact in-progress session and sets logged prior to kill.

### F. Room Migration Tests
- **Location**: `src/androidTest/java/com/spoton/data/local/migration/`
- **Framework**: Room `MigrationTestHelper`.
- **Mandatory Test Cases**: Verify every database version bump migrates existing SQLite tables without data loss or column corruption.

---

## 2. Definition of Done (DoD)

A development task or feature implementation is **DONE** if and only if ALL of the following criteria are satisfied:

1. **Compilation**: Code compiles cleanly with zero errors and zero new compiler warnings.
2. **Layer Boundaries**: Code satisfies layer boundaries defined in `ARCHITECTURE.md` and `ENGINEERING_RULES.md`.
3. **Automated Unit Tests**: All relevant engine unit tests and ViewModel tests pass.
4. **DAO & Integration Tests**: All Room DAO queries pass in-memory Android tests.
5. **Process Death Verified**: Resume-safe logging verified via process recreation.
6. **Emulator Verification**: Manually executed and verified on an Android Emulator running API 26 or higher.
7. **Documentation Sync**: `CURRENT_STATE.md`, `FEATURE_SCOPE.md`, and `GAP_REGISTER.md` updated reflecting work completed.
