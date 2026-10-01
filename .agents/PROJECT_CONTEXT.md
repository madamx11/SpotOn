Purpose: Define the core mission, problem statement, target user profile, key design principles, success criteria, and explicit non-goals of SpotOn.

# Project Context: SpotOn

## 1. Executive Summary

SpotOn is an Android-only gym workout tracking application built strictly for frictionless, one-handed execution in poor-connectivity gym environments. It eliminates unnecessary overhead (templates, splits, social feeds, cloud sync) to focus purely on rapid set logging and clear progression feedback.

---

## 2. Problem Statement & Target User

### User Profile
- **Frequency**: Trains ~5 days per week.
- **Volume**: Targets ~3 body parts per session.
- **Behavior**: Exercise weights and rep counts fluctuate session to session (up, same, or down).
- **Pain Point**: Cannot effectively track set-by-set progression using traditional paper notepads or mobile spreadsheets due to friction, slow data entry, and lack of real-time weight-specific comparison.

### Environment & Constraints
- **Setting**: Active gym floor.
- **Connectivity**: Poor or absent cellular signal (must be 100% offline-first).
- **Physical Context**: One-handed operation between sets while fatigued.

---

## 3. Core Principle

> **Frictionless logging first, analytics second.**

The app remembers everything from past workouts so the user doesn't have to. The user only inputs what was just executed. In standard usage, the user types reps on a large numeric keypad and taps a checkmark.

---

## 4. Domain Hierarchy

SpotOn enforces a flat, modular domain hierarchy:

```
MuscleGroup  -->  Exercise  -->  Session  -->  SetEntry
```

- **MuscleGroup**: Anatomical target (e.g., Chest, Back, Quads).
- **Exercise**: Specific movement mapped to one MuscleGroup (e.g., Barbell Bench Press).
- **Session**: Bound to one Exercise on one calendar date (`Session = Exercise + Date`). There are NO workout splits, NO workout days, and NO workout templates.
- **SetEntry**: Individual performance unit (weight, reps, set type, RIR, timestamp) linked to a Session.

---

## 5. Success Criteria

| Metric | Target / Standard |
|---|---|
| **Logging Speed** | Complete set logging in **≤ 2 seconds** post-set. |
| **Ergonomics** | 100% of logging interactions operable **one-handed** (thumb zone). |
| **Offline Reliability** | **Zero latency** and zero data loss in zero-signal environments. |
| **Data Integrity** | Instant persistence per set; zero data loss on process death or screen sleep. |
| **Progression Feedback** | Immediate set-level performance comparison against prior session at the exact same weight. |

---

## 6. Explicit Non-Goals & Out of Scope (V1 Core)

The following features are strictly out of scope for initial development:

- **iOS Support**: Android only (Min SDK 26).
- **Workout Templates / Splits / Days**: Sessions are strictly per-exercise per-date.
- **Rest Timers**: No countdown timers or audio alerts.
- **Social Features**: No feeds, sharing, friends, or leaderboards.
- **Gamification**: No streaks, badges, points, or virtual rewards.
- **AI Workout Generation**: No automated routine creation.
- **Nutrition / Calorie Tracking**: Focus is exclusively on strength resistance logging.
- **User Accounts / Auth**: No sign-in, login screens, or cloud profiles.
- **Cloud Sync**: 100% local database storage (V1).
