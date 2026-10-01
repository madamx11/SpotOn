Purpose: System architecture specification detailing Android tech stack, package structure, layer boundaries, MVVM + UDF patterns, data flow, and offline-first Room persistence.

# Technical Architecture

## 1. Core Technology Stack

| Component | Technology | Specification / Details |
|---|---|---|
| **Platform** | Android Native | Min SDK 26 (Android 8.0+), Single Activity architecture. |
| **Language** | Kotlin | Standard library + Kotlin Coroutines & Flow. |
| **UI Framework** | Jetpack Compose | Material 3 design tokens, Navigation Compose for routing. |
| **Dependency Injection** | Hilt | Standard Android DI annotations (`@HiltAndroidApp`, `@Inject`, `@Module`). |
| **Database** | Room | SQLite single source of truth, offline-first data flow. |
| **Widget Framework** | Jetpack Glance | Home-screen quick logging widget (V3). |
| **Background Work** | WorkManager | Scheduled jobs (auto-backup, weekly recap calculations). |
| **Preferences** | DataStore | Key-value settings storage (e.g., unit preference, stepper increments). |
| **Canonical Units** | Kilograms (kg) | All weights stored and calculated in `kg`. Converted to `lb` at UI rendering edge. |

---

## 2. Architectural Pattern: Layered MVVM with UDF

SpotOn enforces strict unidirectional data flow (UDF) across three clean architectural layers:

```
+-------------------------------------------------------------------+
|                            UI LAYER                               |
|   Composables  <--- (StateFlow<UiState>) ---  ViewModels          |
|                --- (User Events / Intent) -->                     |
+-------------------------------------------------------------------+
                                  |
                                  v
+-------------------------------------------------------------------+
|                          DOMAIN LAYER                             |
|   UseCases / Insight Engine Modules (Pure Kotlin - Zero Android) |
+-------------------------------------------------------------------+
                                  |
                                  v
+-------------------------------------------------------------------+
|                           DATA LAYER                              |
|   Repositories  --->  Room DAOs / DataStore (Offline-First)       |
+-------------------------------------------------------------------+
```

### Layer Boundaries & Responsibilities

1. **UI Layer (`com.spoton.ui`)**:
   - Consists of Compose screens, reusable components, and ViewModels.
   - Observes `StateFlow<UiState>` emitted by ViewModels.
   - Emits user actions (e.g., `LogSetIntent`, `UpdateRepsIntent`) to ViewModel.
   - **Constraint**: UI NEVER accesses Room DAOs or performs calculations.

2. **Domain Layer (`com.spoton.domain`)**:
   - Contains pure Kotlin business models, repository interfaces, and the deterministic **Insight Engine**.
   - **Constraint**: Pure Kotlin modules ONLY. Must have zero `android.*` dependencies.

3. **Data Layer (`com.spoton.data`)**:
   - Room Database (`SpotOnDatabase`), Entity definitions, DAOs, and DataStore implementations.
   - Implements repository interfaces defined in the domain layer.
   - Serves as the single source of truth (`Room` DB).

---

## 3. Package Structure

```
com.spoton/
├── MainActivity.kt
├── SpotOnApplication.kt
├── data/
│   ├── local/
│   │   ├── dao/
│   │   ├── entity/
│   │   ├── SpotOnDatabase.kt
│   │   └── DataStoreManager.kt
│   └── repository/
├── domain/
│   ├── engine/          # Insight Engine calculation modules
│   ├── model/           # Pure domain models
│   ├── repository/      # Repository interfaces
│   └── usecase/         # Domain use cases
├── ui/
│   ├── components/      # Reusable Compose widgets
│   ├── navigation/      # NavHost and screen routes
│   ├── theme/           # Compose M3 theme, colors, typography
│   └── feature/         # Feature screens & ViewModels
│       ├── catalog/
│       ├── logger/
│       ├── dashboard/
│       └── settings/
└── di/                  # Hilt modules
```

---

## 4. Unidirectional Data Flow (UDF) & State Rules

- Each screen ViewModel exposes exactly **one** `StateFlow<ScreenUiState>`.
- UI State classes must be immutable data classes or sealed interfaces (e.g., `Loading`, `Success`, `Error`).
- State updates are processed sequentially via Coroutines and published atomically to the `StateFlow`.
- Set persistence is synchronous with user checkmark taps: every set is saved immediately to Room upon completion.
