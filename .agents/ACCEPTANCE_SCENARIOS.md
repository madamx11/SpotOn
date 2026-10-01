Purpose: Executable Given/When/Then acceptance test scenarios for functional validation of key SpotOn features.

# Feature Acceptance Scenarios

These Given/When/Then scenarios serve as functional contract acceptance criteria for SpotOn features.

---

## Scenario 1: Fast Set Logging with Pre-Filled Weight

```gherkin
Given the user previously completed Set 1 of "Barbell Bench Press" at 80.0 kg for 10 reps
When the user opens a new session for "Barbell Bench Press" and taps "Log Next Set"
Then the weight input field automatically pre-fills with "80.0" kg
And the cursor defaults to the reps input field
When the user enters "10" on the numeric keypad and taps the checkmark button
Then the set is saved in <= 2 seconds
And the screen displays Set 1 as completed with weight 80.0 kg and 10 reps.
```

---

## Scenario 2: Comparison at Same Weight When Set Counts Differ

```gherkin
Given in the PREVIOUS session, the user performed:
  | Set Index | Weight | Reps |
  | 1         | 60 kg  | 12   |
  | 2         | 80 kg  | 8    |
  | 3         | 80 kg  | 6    |
When in the CURRENT session, the user performs Set 1 at 80 kg for 9 reps
Then the feedback engine compares 80 kg against the FIRST set performed at 80 kg in the previous session (Set 2: 8 reps)
And the UI displays a green badge "+1 rep @ 80.0 kg"
And does NOT compare against Set 1 (60 kg) or by set position index.
```

---

## Scenario 3: Warm-Up Exclusion from Metrics

```gherkin
Given a user logs 2 warm-up sets (60 kg x 10 reps, WARMUP) and 3 working sets (100 kg x 8 reps, NORMAL)
When the user views the Exercise History Graph and Muscle Group Dashboard
Then the total working volume counts ONLY the 3 working sets (2400 kg)
And the warm-up sets are strictly excluded from volume, hard set counts, and trend scores.
```

---

## Scenario 4: Rep PR Detection

```gherkin
Given the user's historical best reps at 100.0 kg on "Squat" is 8 reps
When the user logs a working set of "Squat" at 100.0 kg for 9 reps
Then the system detects a new Rep PR
And displays a "PR! Best reps at 100.0 kg" gold badge
And triggers a distinct PR haptic vibration pattern.
```

---

## Scenario 5: Double-Progression Suggestion

```gherkin
Given "Incline Dumbbell Press" has targetRepMin = 8 and targetRepMax = 12
When the user completes a session where ALL working sets hit 12 reps at 30.0 kg
Then upon completing the session, the app displays a Double-Progression Prompt:
  "Target hit on all sets! Consider increasing weight to 32.5 kg next session."
```

---

## Scenario 6: Plateau Flag Detection

```gherkin
Given an exercise has 4 consecutive completed sessions with no increase in weight or reps at equivalent weight
When the user completes the 4th session
Then the exercise detail screen flags a "Plateau Detected" status banner
And prompts the user to review technique, RIR, or session notes.
```

---

## Scenario 7: App Killed Mid-Session and Resumed

```gherkin
Given the user is mid-session on "Overhead Press" and has logged 2 of 4 sets
When the Android OS kills the app process due to low memory or screen sleep
And the user re-opens SpotOn
Then the application immediately restores the exact active "Overhead Press" session
And display shows Set 1 and Set 2 intact with all previously logged values.
```

---

## Scenario 8: Fully Offline Usage

```gherkin
Given the Android device is in Airplane Mode with 0 cellular signal and zero Wi-Fi
When the user creates exercises, logs sets, views graphs, and edits sessions
Then 100% of features function with zero latency, zero errors, and zero data loss.
```

---

## Scenario 9: Back-Dating a Session

```gherkin
Given the user forgot to log yesterday's workout
When the user selects "Log Session" and changes the date picker to yesterday's date
And logs 3 sets for "Pull-Ups"
Then the session is stored with yesterday's epoch timestamp
And historical graphs reflect the back-dated session in correct chronological order.
```

---

## Scenario 10: Archiving an Exercise Keeps History

```gherkin
Given "Dumbbell Flyes" has 15 historical sessions logged in the database
When the user selects "Archive Exercise" on "Dumbbell Flyes"
Then `isArchived` is set to `true`
And "Dumbbell Flyes" is hidden from active exercise selection dropdowns
And all 15 historical sessions and sets remain completely intact in the database and overall muscle group historical totals.
```

---

## Scenario 11: Merging Duplicate Exercises

```gherkin
Given the database contains duplicate exercises "Bench Press" (ID 1) and "Flat Bench Press" (ID 2)
When the user executes "Merge Exercise" combining ID 2 into ID 1
Then all sessions previously linked to exercise ID 2 are updated to reference exercise ID 1
And exercise ID 2 is soft-archived
And an audit record is written to `change_history`
And no sets or historical sessions are deleted.
```

---

## Scenario 12: Widget Quick-Log (V3)

```gherkin
Given the user has added the SpotOn Glance Quick-Log Widget to the Android launcher screen
When the user taps the "Log Set" button on the widget
Then a lightweight logging dialog opens
And entering reps updates the active session in Room immediately
And the widget UI updates to display the newly logged set count.
```
