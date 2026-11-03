# ViewModel tutorial: Counter + History Tracker

This mini tutorial introduces the core idea of a `ViewModel` in Android with Jetpack Compose.

## App idea

We will build a small app with:

- a counter value
- buttons to increase and decrease the count
- a reset button
- a history list showing the values the user generated

The app is intentionally simple, but it demonstrates the real value of a `ViewModel`:

- the screen does not own all the logic
- the state survives configuration changes such as rotation
- the UI remains cleaner and easier to reason about

## Learning goals

By the end of this tutorial, you will understand:

- what a `ViewModel` is
- why UI state should live outside the screen
- how `ViewModel` survives rotation
- how to connect Compose UI to a `ViewModel`
- how to separate business logic from presentation

## Tutorial structure

1. [01-project-setup.md](01-project-setup.md) — create the project and basic screen
3. [02-viewmodel-logic.md](02-viewmodel-logic.md) — move logic into a `ViewModel`
2. [03-counter-screen.md](03-counter-screen.md) — build the UI for the counter
4. [04-history-tracker.md](04-history-tracker.md) — add the value history list

## Why this app is a good example

While the counter is easy to understand, it contains the same design pattern you will use in bigger apps:

- your UI displays state
- user actions trigger events
- the `ViewModel` updates state
- Compose redraws the screen automatically

The app is small enough to learn the pattern without getting lost in unrelated complexity.

---

[Start the tutorial](01-project-setup.md)
