# Activity Lifecycle tutorial

Let's build a `Daily Water Tracker` app.

In this tutorial, you will build an app that helps a person track how much water they have drunk during the day. Data changes as the user interacts with the app.

## Learning goals

By the end of this lesson, students will be able to:
- explain the six lifecycle callbacks of an Activity,
- understand the order in which they run,
- read logs in Android Studio,
- observe what happens on orientation change,
- explain why state must be preserved across rotation.

## Tutorial steps

1. [01-user-interface.md](01-user-interface.md) — build the useful app and its main screen
2. [02-lifecycle-events.md](02-lifecycle-events.md) — log all six lifecycle callbacks with Logcat
3. [03-rotation-and-state.md](03-rotation-and-state.md) — rotate the device and explain recreation + state preservation

## Main lifecycle events

- `onCreate()`
- `onStart()`
- `onResume()`
- `onPause()`
- `onStop()`
- `onDestroy()`

## New tools

- Logcat
- Device rotation simulation

---

Start with [01-water-tracker-app.md](01-user-interface.md)
