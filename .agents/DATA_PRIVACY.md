Purpose: Security policy, data storage rules, permission management, backup/export security, and corruption handling guidelines.

# Data Privacy & Local Security Policy

SpotOn prioritizes absolute user data privacy. The application operates under a **100% Local-First Data Posture**.

---

## 1. Local-Only Data Posture

- **Zero Cloud Servers**: SpotOn operates with zero remote backends, databases, or cloud endpoints in V1.
- **Zero User Accounts**: No registration, email collection, authentication, or identity tracking.
- **Zero Telemetry & Analytics**: No third-party tracking SDKs (e.g., Firebase Analytics, Adjust, AppsFlyer) are compiled into the application.
- **Zero Network Usage**: The app does not transmit any user training, body weight, or usage data off the physical Android device.

---

## 2. Android Permissions Policy

SpotOn adheres to the principle of least privilege:

| Permission | Purpose | Requested Version |
|---|---|---|
| `INTERNET` | **STRICTLY PROHIBITED**. Must NOT exist in `AndroidManifest.xml`. | None |
| `POST_NOTIFICATIONS` | Sending local weekly recap summaries. | V3 (Runtime request) |
| `VIBRATE` | Triggering haptic feedback on set completion. | V1 (Normal permission) |

No storage read/write permissions are required on Android 10+ (API 29+) as file backup and export features exclusively use the system **Storage Access Framework (SAF)** picker.

---

## 3. Data Backup, Export & Portability

1. **Export Format**: Data exports produce human-readable, standard UTF-8 `CSV` files containing exercises, sessions, sets, and body weight logs.
2. **Storage Location**: Users select their own destination directory (e.g., local downloads folder or personal cloud drive) via Android SAF.
3. **Backup Encryption**: Local database backups created via SAF are stored in standard SQLite format. The user retains complete physical ownership of backup files.

---

## 4. Restore Validation & Corruption Handling

To protect user history during backup restore or CSV import actions:

1. **Pre-Import Schema Validation**: Before overwriting database records, the import engine MUST validate header rows, column data types, foreign key consistency, and schema version numbers.
2. **Atomic Transactions**: Database restore operations MUST execute inside a single Room database transaction (`runInTransaction`). If any record fails validation, the entire transaction rolls back cleanly to preserve existing data.
3. **Automatic Pre-Restore Snapshot**: Before executing a database restore, SpotOn automatically creates a local database snapshot file (`spoton_pre_restore_bak.db`).

---

## 5. Logging Restrictions (What Must Never Be Logged)

To prevent accidental data exposure in system `Logcat`:

- **PROHIBITED IN LOGS**: User exercise notes, session notes, body weight entries, exact set details, and personal file paths MUST NEVER be printed to Android `Logcat` or system logs.
- **ALLOWED IN LOGS**: Generic operational debug events (e.g., `Database initialized successfully`, `Navigation triggered: Catalog -> Logger`) using `Timber` or standard `Log` wrappers in `DEBUG` builds only.
