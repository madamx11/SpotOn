Purpose: Android Compose UX rules, dark theme design system, typography, layout patterns, one-handed gym usability, and feedback mechanisms.

# UI & UX Guidelines

SpotOn UI is designed for high-stress, low-focus gym environments. The UI MUST prioritize speed, high contrast, physical ergonomics, and instant visual feedback over decorative fluff.

---

## 1. Ergonomics & One-Handed Layouts

```
+-----------------------------------+
| Top App Bar (Exercise Name)       |
+-----------------------------------+
| Historical Set Feedback Banner    |
| (Same-Weight Comparison Display)  |
+-----------------------------------+
| Active Set Rows / List            |
|                                   |
+-----------------------------------+  <-- Natural Thumb Zone
| Large Weight & Rep Steppers       |
| Custom Large Numeric Keypad       |
| [ CHECKMARK LOG SET (Primary) ]   |
+-----------------------------------+
```

1. **Thumb-Zone Optimization**: All primary logging controls (keypad, weight steppers, checkmark action button) MUST be anchored within the bottom 50% of the screen (the natural thumb reach zone).
2. **Minimum Tap Targets**: All interactive buttons, steppers, and checkmarks MUST have a touch target size of **at least 56dp x 56dp** to prevent accidental mis-taps during exercise fatigue.

---

## 2. Visual Theme & Dark Mode First

1. **Dark Theme Native**: SpotOn uses a dark-first Material 3 color palette (`Background: #121212`, `Surface: #1E1E1E`, `Primary: #00E676` / Vibrant Emerald Green). Deep dark backgrounds preserve battery life on OLED screens and minimize screen glare in dark gym settings.
2. **High Contrast Typography**:
   - Primary numbers (reps, weight values): Extra Large Bold Typography (e.g., 36sp to 48sp Monospace / Heavy Sans).
   - Secondary labels: High contrast white/off-white text (`#FFFFFF` / `#E0E0E0`).

---

## 3. Dual-Channel Feedback Guidelines

Visual indicators MUST NEVER rely on color alone to communicate performance feedback (e.g., green vs red text).

| Metric Feedback State | Required Visual Indicator | Required Text Label | Haptic Pattern |
|---|---|---|---|
| **Increased Reps** | Green Up Arrow Icon `▲` | `+N reps @ X kg` | Double light tap |
| **Decreased Reps** | Amber Down Arrow Icon `▼` | `-N reps @ X kg` | Single short pulse |
| **Same Reps** | Gray Equals Icon `=` | `Same @ X kg` | Light click |
| **New Rep PR** | Gold Star / Trophy Badge `★` | `PR! Best reps @ X kg` | Strong success vibration |

---

## 4. Navigation & Friction Reduction

1. **Minimal Tap Flow**: Standard logging flow MUST be completed in minimal taps:
   ```
   Home Dashboard  --(1 Tap)-->  Exercise Logger  --(Type Reps + 1 Tap)-->  Set Logged
   ```
2. **Keep-Screen-Awake**: While the user is actively on the set logging screen, the screen MUST be kept awake (`FLAG_KEEP_SCREEN_ON`).

---

## 5. UI States & Accessibility Basics

1. **Empty States**: Every list (Exercises, Sessions, Graph history) MUST display a helpful, high-contrast empty state with a clear call-to-action button (e.g., *"No exercises in Chest yet. Tap + to add Barbell Bench Press"*).
2. **Loading States**: Use subtle skeleton shimmers rather than screen-blocking progress dialogs.
3. **Accessibility**: Every icon button MUST include an explicit `contentDescription` string resource.
