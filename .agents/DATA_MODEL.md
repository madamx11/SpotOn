Purpose: Formal database schema, entity definitions, constraints, indices, relationships, and invariant business rules for SpotOn.

# SpotOn Data Model

All persistent application data is stored locally in Room (SQLite). All v1 schema tables (`muscle_groups`, `exercises`, `sessions`, `set_entries`, `change_history`) exist from Part 1 so all columns needed by later parts exist from Part 1 to avoid unnecessary migrations. `body_weight_entries` may be added in Part 7 via migration.

---

## 1. Schema Entities

### A. MuscleGroup Table (`muscle_groups`)
Represents an anatomical target area. Pre-seeded standard muscle groups: Chest, Back, Shoulders, Biceps, Triceps, Forearms, Legs, Abs.

| Field | Type | Nullable | Constraints / Default | Description |
|---|---|---|---|---|
| `id` | `Long` | No | Primary Key, Auto-increment | Unique identifier. |
| `name` | `String` | No | Unique index | Name of muscle group. |
| `sortOrder` | `Int` | No | Default `0` | Display sorting priority. |
| `isArchived` | `Boolean` | No | Default `false` | Soft-deletion flag. |

### B. Exercise Table (`exercises`)
Represents a specific movement bound to a single `MuscleGroup`.

| Field | Type | Nullable | Constraints / Default | Description |
|---|---|---|---|---|
| `id` | `Long` | No | Primary Key, Auto-increment | Unique identifier. |
| `muscleGroupId` | `Long` | No | Foreign Key -> `muscle_groups(id)` ON DELETE RESTRICT | Associated muscle group. |
| `name` | `String` | No | Unique index per muscle group | Exercise name. |
| `equipmentType` | `String` | No | Enum: `BARBELL`, `DUMBBELL`, `MACHINE`, `CABLE`, `BODYWEIGHT` | Equipment categorization. |
| `isPerSide` | `Boolean` | No | Default `false` | `true` if weight is per side (e.g. unilateral dumbbell). |
| `targetRepMin` | `Int` | No | Default `8` | Lower boundary of target rep range. |
| `targetRepMax` | `Int` | No | Default `12` | Upper boundary of target rep range. |
| `notes` | `String` | Yes | Default `null` | Persistent form cues/setup notes. |
| `sortOrder` | `Int` | No | Default `0` | Sorting order within muscle group. |
| `isArchived` | `Boolean` | No | Default `false` | Soft-deletion flag. |

### C. Session Table (`sessions`)
Represents a workout instance for ONE exercise on ONE calendar date. Entity schema: `Session (id, exerciseId, date, tags, note, gym nullable text, isCompleted)`.

| Field | Type | Nullable | Constraints / Default | Description |
|---|---|---|---|---|
| `id` | `Long` | No | Primary Key, Auto-increment | Unique identifier. |
| `exerciseId` | `Long` | No | Foreign Key -> `exercises(id)` ON DELETE RESTRICT | Target exercise. |
| `date` | `Long` | No | Epoch millis (start of day UTC) | Date of session execution. |
| `tags` | `String` | Yes | Default `null` (Comma-separated) | Session tags (e.g., "fatigued", "gym-A"). |
| `note` | `String` | Yes | Default `null` | Session-specific execution notes. |
| `gym` | `String` | Yes | Default `null` | Optional gym/location field (stays `null` until Part 6 builds the UI for it). |
| `isCompleted` | `Boolean` | No | Default `false` | Completion status. |

### D. SetEntry Table (`set_entries`)
Represents an individual set performed within a `Session`.

| Field | Type | Nullable | Constraints / Default | Description |
|---|---|---|---|---|
| `id` | `Long` | No | Primary Key, Auto-increment | Unique set identifier. |
| `sessionId` | `Long` | No | Foreign Key -> `sessions(id)` ON DELETE CASCADE | Parent session. |
| `setIndex` | `Int` | No | Indexed within session | Sequential 1-based order in session. |
| `weightKg` | `Double` | No | Must be >= 0.0 | Canonical weight in kilograms. |
| `reps` | `Int` | No | Must be >= 0 | Number of completed repetitions. |
| `type` | `String` | No | Enum: `NORMAL`, `WARMUP`, `DROP`, `FAILURE` | Set classification. |
| `rir` | `Int` | Yes | Nullable (0 to 10) | Reps in reserve rating. |
| `createdAt` | `Long` | No | Epoch millis | Timestamp of set completion. |

### E. BodyWeightEntry Table (`body_weight_entries`)
Represents daily body weight logs (V3).

| Field | Type | Nullable | Constraints / Default | Description |
|---|---|---|---|---|
| `id` | `Long` | No | Primary Key, Auto-increment | Unique identifier. |
| `date` | `Long` | No | Epoch millis (Unique index) | Date of measurement. |
| `weightKg` | `Double` | No | Must be > 0.0 | Body weight in kilograms. |

### F. Settings Entity (DataStore / Preferences)
Application-wide configurations.

- `weightUnit`: Enum (`KG`, `LB`), default `KG`.
- `defaultWeightIncrementKg`: Double, default `2.5`.
- `keepScreenAwake`: Boolean, default `true`.
- `hapticFeedbackEnabled`: Boolean, default `true`.

### G. ChangeHistory Table (`change_history`)
Audit and resolution log for exercise merges and set edits.

| Field | Type | Nullable | Constraints / Default | Description |
|---|---|---|---|---|
| `id` | `Long` | No | Primary Key, Auto-increment | Audit record ID. |
| `timestamp` | `Long` | No | Epoch millis | Action timestamp. |
| `actionType` | `String` | No | Enum: `EDIT_SET`, `DELETE_SET`, `MERGE_EXERCISES` | Performed modification type. |
| `details` | `String` | No | JSON payload | Audit context (original & changed data). |

---

## 2. Invariant Rules & Data Constraints

1. **NO HARD DELETES**: `MuscleGroup` and `Exercise` entities MUST NEVER be hard-deleted from the database. Deletion requests execute soft-archiving (`isArchived = true`). Historical sessions and sets remain attached to archived entities.
2. **Canonical Unit Storage**: All weight values (`weightKg`) in `SetEntry` and `BodyWeightEntry` MUST be stored strictly in kilograms (`Double`). Unit conversion (`kg` to `lb`) is performed exclusively at the UI display layer (`weightKg * 2.20462`).
3. **Session Uniqueness**: A `Session` represents a single exercise on a single date.
4. **Warm-Up Set Exclusion**: Sets where `type == WARMUP` MUST be filtered out from all hard-set counts, working-rep counts, trend scores, PR detection, and progression logic.

---

## 3. Metric & Calculation Definitions

- **Same-Weight Comparison**: Compare set reps against the set at the **same exact `weightKg`** from the preceding completed session.
- **Trend Score Formula**: Calculate the average of each exercise's percentage change against its own 4-session historical baseline:
  $$\text{Trend Score} = \frac{1}{N} \sum_{i=1}^{N} \left( \frac{\text{Current Performance}_i - \text{Baseline}_i}{\text{Baseline}_i} \times 100 \right)$$
  *Never sum raw kilograms lifted across distinct exercises.*
- **Hard Set Definition**: Working set (`NORMAL`, `DROP`, `FAILURE`) executed within target rep range or RIR ≤ 3.
- **Plateau Condition**: 4 consecutive sessions for an exercise with 0% improvement in load or reps at equivalent load.
- **Exercise Performance Graphs**: Graphs show top weight and total working reps over time (and best reps per weight), never weight x reps volume.
