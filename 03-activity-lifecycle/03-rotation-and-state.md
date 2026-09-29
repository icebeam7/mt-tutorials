# Step 3: Rotation and state changes with real persistence

## Goal

In a real app, we often save and read state during the six events so the user does not lose progress.

---

## 1. What happens when the screen rotates?

Rotate the emulator or device.

The app should restart and logs should show something like:

```text
D/WaterTrackerLifecycle: onPause() called. Saved current value before leaving screen: 4
D/WaterTrackerLifecycle: onStop() called. Final save before app becomes invisible: 4
D/WaterTrackerLifecycle: onDestroy() called. Last value saved: 4
D/WaterTrackerLifecycle: onCreate() called. Loaded glasses from storage: 4
D/WaterTrackerLifecycle: onStart() called. Reading latest value from storage: 4
D/WaterTrackerLifecycle: onResume() called. App is interactive. Current glasses: 4
```

This is a very important Android concept: screen rotation **recreates** the Activity. In our case, the app saves state just before leaving the screen and reloads it when needed. So we don't lose any value when the screen is rotated.

> The app does not only log lifecycle events. It also saves the current water count in a small storage file so that when the Activity is recreated, it can read the value back and continue where the user left off.

---

[Back to tutorial home](README.md)
