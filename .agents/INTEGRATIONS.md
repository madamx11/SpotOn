Purpose: Specification of Android system component integration boundaries, platform wrappers, and clean domain isolation rules.

# System Integrations & Android Platform Boundaries

SpotOn interacts with several Android system frameworks. To maintain clean architecture, **the domain layer MUST NEVER depend directly on these Android APIs**. All platform capabilities are wrapped behind clean Kotlin interfaces defined in the domain layer and implemented in `data/` or `ui/`.

---

## Integration Registry

### 1. Jetpack Glance Home-Screen Widget
- **Purpose**: Enables quick set logging directly from the Android home launcher without opening the full application UI.
- **Where It Lives**: `com.spoton.ui.widget` (UI/System boundary).
- **Domain Interface**: `WidgetUpdateRepository` (Domain interface triggered whenever sets/sessions update).
- **Isolation Rule**: Glance AppWidget components consume domain repositories via Hilt `@EntryPoint` injections. Domain code is completely unaware of Glance UI classes.

### 2. WorkManager (Scheduled Background Tasks)
- **Purpose**: Executes periodic background processing (auto-backup execution, weekly recap computation).
- **Where It Lives**: `com.spoton.data.worker` (Data/System boundary).
- **Domain Interface**: `BackgroundScheduler` (Domain interface with methods like `scheduleAutoBackup()`, `scheduleWeeklyRecap()`).
- **Isolation Rule**: WorkManager `ListenableWorker` implementations delegate work directly to Domain Use Cases.

### 3. DataStore (Preferences Storage)
- **Purpose**: Stores lightweight key-value settings (e.g., active weight unit `KG`/`LB`, default stepper increment `2.5kg`, haptics enabled).
- **Where It Lives**: `com.spoton.data.local.DataStoreManager`.
- **Domain Interface**: `SettingsRepository` (Exposes `Flow<UserSettings>`).
- **Isolation Rule**: Domain components consume `UserSettings` domain models; `PreferencesDataStore` imports stay in `data/`.

### 4. Storage Access Framework (SAF)
- **Purpose**: Provides user-selected directory access for CSV data exports, CSV imports, and database backup file writing.
- **Where It Lives**: `com.spoton.ui.system` & `com.spoton.data.backup`.
- **Domain Interface**: `FileStorageExporter` and `FileStorageImporter`.
- **Isolation Rule**: SAF `Uri` handling occurs at the UI/Activity level. Raw input/output streams are passed into domain use cases as standard Kotlin `InputStream`/`OutputStream`.

### 5. Notifications
- **Purpose**: Dispatches local notifications for weekly recaps or auto-backup status (V3).
- **Where It Lives**: `com.spoton.ui.notification.NotificationManagerWrapper`.
- **Domain Interface**: `NotificationNotifier` interface.
- **Isolation Rule**: Domain passes notification content objects (`Title`, `Body`) to `NotificationNotifier`. Android `NotificationCompat` code is strictly isolated in the integration wrapper.

### 6. Android Auto Backup
- **Purpose**: Allows standard Android system cloud/device migration backups of local SQLite Room DB files.
- **Where It Lives**: `AndroidManifest.xml` manifest configurations (`android:allowBackup="true"`).
- **Domain Interface**: None. Handled transparently by OS.

### 7. Haptic Feedback
- **Purpose**: Provides subtle physical vibration cues upon set completion checkmark taps or stepper adjustments.
- **Where It Lives**: `com.spoton.ui.haptics.HapticFeedbackHelper`.
- **Domain Interface**: None (UI-only sensory feedback).
- **Isolation Rule**: Invoked directly within Jetpack Compose click handlers using `LocalHapticFeedback.current` or `Vibrator` helper wrappers.

### 8. Keep-Screen-Awake
- **Purpose**: Prevents the display from sleeping or locking while the user is on the active logging screen in the gym.
- **Where It Lives**: `com.spoton.ui.feature.logger.DisposableKeepScreenOnEffect`.
- **Domain Interface**: None (UI state boundary).
- **Isolation Rule**: Implemented in Compose using `DisposableEffect` toggling `FLAG_KEEP_SCREEN_ON` on the host Activity window context.
