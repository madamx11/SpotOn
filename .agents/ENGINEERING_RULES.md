Purpose: Hard rules for writing code, architectural boundaries, package structure, state management, database migrations, and dependency policies.

# Engineering Rules

Every code modification in SpotOn MUST comply with these non-negotiable rules.

---

## 1. Architectural & Layering Rules

1. **Strict Layer Separation**:
   - **UI Layer** (`ui/`): Composables, ViewModels, UI state. Imports domain layer ONLY. NEVER imports `data/` or DAOs directly.
   - **Domain Layer** (`domain/`): Pure Kotlin entities, repository interfaces, use cases, and Insight Engine modules. MUST NOT import `android.*` framework APIs or `data/` concrete classes.
   - **Data Layer** (`data/`): Room entities, DAOs, Database, DataStore, Repository implementations.
2. **Calculation Isolation**:
   - ALL metric calculations, comparison logic, progression prompts, and trend formulas MUST live strictly in `com.spoton.domain.engine.*`.
   - ViewModel classes, Composables, and Jetpack Glance Widgets MUST NEVER execute arithmetic metric comparisons or custom trend formulas.
3. **Single Source of Truth**:
   - Room SQLite is the sole source of truth for app state. UI state is a reactive stream (`Flow`) derived directly from Room persistence.

---

## 2. Data Persistence & Unit Rules

1. **Instant Set Persistence**:
   - Every logged set MUST be written to Room immediately upon user tap (`SaveSetUseCase`). Unsaved draft states in memory are prohibited.
   - App termination, process death, or screen sleep MUST restore the exact in-progress session state seamlessly.
2. **Canonical Unit Rule**:
   - All database columns and domain models store weights exclusively in kilograms (`weightKg: Double`).
   - Conversion to pounds (`lb`) occurs ONLY at the UI formatting boundary (`weightKg * 2.20462`).
3. **No Hard Deletion**:
   - Never execute SQL `DELETE` on `muscle_groups` or `exercises`. Execute soft-archiving (`isArchived = true`).

---

## 3. Network & Security Restrictions

1. **Zero Network Calls**: SpotOn V1 is 100% offline-first.
2. **No INTERNET Permission**: The `<uses-permission android:name="android.permission.INTERNET" />` tag is STRICTLY PROHIBITED in `AndroidManifest.xml`.
3. **No Third-Party Analytics / Tracking**: No SDKs for telemetry, crash reporting, or user tracking (e.g., Firebase Analytics, Mixpanel, Sentry) may be added in V1.

---

## 4. Dependency Injection & State Management Rules

1. **Hilt Dependency Injection**:
   - All ViewModels must use `@HiltViewModel`.
   - All repository implementations must be bound via Hilt modules using `@Binds` or `@Provides` with appropriate scopes (`@Singleton`).
2. **Single UI State per Screen**:
   - ViewModels expose exactly ONE immutable `StateFlow<UiState>` per screen.
   - UI State must use explicit data representation (Sealed Interface: `Loading`, `Content`, `Error`).
3. **Unidirectional Data Flow (UDF)**:
   - UI emits user intents (e.g., `SetLoggedIntent`) to ViewModel.
   - ViewModel invokes Domain Use Cases.
   - Use Cases update Repository.
   - Repository emits updated state via Flow to ViewModel.

---

## 5. Database Migration Rules

1. **Never Destructive**: `fallbackToDestructiveMigration()` is STRICTLY FORBIDDEN in production Room configurations.
2. **Explicit Migrations**: Every database schema change MUST provide a explicit `Migration(oldVersion, newVersion)` implementation.
3. **Migration Tests**: Every schema bump MUST be accompanied by an automated Room migration test verifying zero data loss.

---

## 6. Dependency Management Policy

1. **No Unapproved Dependencies**: No new external libraries or Gradle dependencies may be added without explicit user approval.
2. **Documentation Requirement**: Any new dependency added MUST be logged in `.agents/CURRENT_STATE.md` under `Dependencies Added` with rationale.

---

## 7. Naming & Formatting Conventions

- **Classes & Interfaces**: PascalCase (`SetComparisonEngine`, `ExerciseRepository`).
- **Functions & Variables**: camelCase (`calculateTrendScore`, `weightKg`).
- **Compose Functions**: PascalCase (`SetLoggerScreen`, `WeightStepper`).
- **Room Entities & Tables**: Lowercase snake_case for tables (`set_entries`, `muscle_groups`).

---

## 8. Summary of Standing Agent Protocol

1. **Read Rules First**: Consult `.agents/README.md` and read mandatory files before writing any code.
2. **Scope Verification**: Verify task against `FEATURE_SCOPE.md` and `DEVELOPMENT_ORDER.md`.
3. **Plan First**: Output a concise implementation plan before starting code edits.
4. **Small Steps**: Make minimal, verifiable edits. Do not refactor unrelated files.
5. **Post-Task Updates**: Run tests, update `CURRENT_STATE.md`, `FEATURE_SCOPE.md`, and `GAP_REGISTER.md`, then report verification steps.
