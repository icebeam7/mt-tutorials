# Part 5 - Demo

[Previous: Pass Arguments](04-navigation.md) | [Next: Additional Features](06-additional-features.md)

### Display `StudyPlannerApp` from `onCreate`

Inside `onCreate`, find the existing `setContent { ... }` block. Replace the current screen call with the line below, keeping it inside your existing app theme.

```kotlin name=MainActivity.kt
StudyPlannerApp()
```

This makes `StudyPlannerApp` the root composable, allowing its navigation graph to choose which screen is displayed.

### Check the navigation

Run the app and try the following:

1. Select a task on the home screen.
2. Confirm that its details appear.
3. Toggle its completion status.
4. Tap **Back** and confirm that the task row shows the updated status.

[Previous: Pass Arguments](04-navigation.md) | [Next: Additional Features](06-additional-features.md)
