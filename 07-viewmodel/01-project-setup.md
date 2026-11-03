# 01 — Project setup

In this first step, we create a simple Compose project and prepare the files for the counter app.

## What we are building

We want a screen like this:

- a number displayed in the middle
- a `+` button
- a `-` button
- a `Reset` button
- a small history list below

The important idea is that the number and history should live in a `ViewModel`, not directly inside the activity or composable.

## Start a new project

Open Android Studio and create a new project:

- Empty Activity
- Name: `CounterHistoryApp`
- Language: Kotlin
- Use Compose default template

This gives you a default `MainActivity.kt` and Compose setup.

---

[Next: 02 — Implement the ViewModel logic](02-viewmodel-logic.md)
