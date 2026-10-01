Purpose: Comprehensive feature inventory, scope boundaries, version mapping (V1/V2/V3), implementation status, and out-of-scope directives.

# Feature Scope & Version Matrix

Build order in DEVELOPMENT_ORDER.md always takes precedence over the version labels (V1/V2/V3), which only indicate product priority.

No feature may be planned, designed, or built unless it is explicitly listed in this table. Any unlisted feature requested by prompt MUST be formally added to this document before code is written.

---

## Feature Inventory

| Feature | Version | Status | Notes |
|---|---|---|---|
| Muscle Group & Exercise Catalog | V1 | Not Started | CRUD, custom sort order, soft-archiving (never hard-delete). |
| Core Set Logger | V1 | Not Started | Pre-fill weight, +/- steppers (default 2.5kg), one-tap "same as last time", warm-up toggle. |
| Last-Session Feedback Engine | V1 | Not Started | Same-weight comparison vs prior session set (+N/-N/same reps). |
| Resume-Safe Logging | V1 | Not Started | Instant Room persistence per set, keep-screen-awake while logging. |
| Exercise Notes | V1 | Not Started | Persistent text notes per exercise and per session. |
| Exercise Performance Graphs | V1 | Not Started | Visualizing top weight and total working reps over time (and best reps per weight, never weight x reps volume). |
| Muscle Group Dashboard | V1 | Not Started | Hard sets per week, trend score (% change vs baseline), days since trained. |
| Set Types | V2 | Not Started | Support for normal, warmup, drop, and failure sets. |
| Reps in Reserve (RIR) | V2 | Not Started | Nullable RIR logging per set entry. |
| Rep PR Detection | V2 | Not Started | Indicator for highest reps achieved at a given weight. |
| Double-Progression Suggestions | V2 | Not Started | Weight increment prompts when top rep range is achieved on all working sets. |
| Plateau Diagnosis | V2 | Not Started | Flagging exercises with no improvement across 4 consecutive sessions. |
| Push/Pull Muscle Balance | V2 | Not Started | Comparative hard set and working rep distribution analysis. |
| Session Tags | V2 | Not Started | Tagging sessions (e.g., fatigue, gym location). |
| Back-Dating & Exercise Merging | V2 | Not Started | Logging historical sessions; merging duplicate exercises while preserving sets. |
| Home-Screen Widget Quick-Log | V3 | Not Started | Glance widget for rapid logging from Android launcher. |
| Multi-Gym Tags | V3 | Not Started | Location/equipment tagging across gym venues. |
| Body Weight Tracking | V3 | Not Started | Daily body weight entries and timeline tracking. |
| Weekly Recap | V3 | Not Started | Automated weekly hard set, working rep, and intensity summary report. |
| Consistency Heatmap | V3 | Not Started | Visual activity calendar grid. |
| Auto-Backup & CSV Export/Import | V3 | Not Started | Storage Access Framework (SAF) local database backups and CSV data portability. |

---

## Explicitly Out of Scope (All Versions)

The following capabilities are prohibited from implementation across all project versions:

1. **iOS Application**: SpotOn is strictly Android-native.
2. **Rest Timers**: No in-app timers, alarms, or countdown overlays.
3. **Social & Community Features**: No user profiles, sharing, feeds, or social integrations.
4. **Gamification**: No streaks, points, levels, achievements, leaderboards, or reward systems. A PR indicator shown on a set is an informational data label, not a gamified badge, and is allowed.
5. **AI Plan Generation**: No automated workout planning or LLM routine recommendations.
6. **Nutrition & Calorie Tracking**: No food database, macro logging, or calorie counts.
7. **User Accounts & Cloud Sync (V1-V3)**: No remote auth, network servers, or cloud synchronization in current roadmap.
