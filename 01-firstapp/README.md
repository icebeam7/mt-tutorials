# Introductory Android Development with Kotlin

In this tutorial you will build a small **Student Tasks** Android application using:

- **Android Studio**
- **Kotlin**
- **Jetpack Compose**
- **Material 3**
- **Navigation Compose** in the final section

The app will evolve from a simple screen into an application where the user can:

1. See tasks.
2. Add a task.
3. Mark tasks as completed.
4. Delete tasks.
5. Navigate between a task list and an Add Task screen.

---

## Learning objectives

By the end of the tutorial, you should be able to:

- Create and run an Android project in Android Studio.
- Recognize the most important files in a basic Android project.
- Use essential Kotlin constructs in an Android application.
- Create UI elements with Jetpack Compose.
- Arrange elements using `Column`, `Row`, and `Modifier`.
- Handle button clicks and text input.
- Understand state and recomposition.
- Represent application data with a Kotlin `data class`.
- Display collections using `LazyColumn`.
- Update immutable UI state.
- Implement simple navigation between screens.
- Use the emulator and Logcat for basic debugging.

---

## Tutorial structure

| Part | Topic | Main outcome |
|---|---|---|
| 1 | Android Studio and first app | Run the first Compose app |
| 2 | Kotlin essentials | Model a task with Kotlin |
| 3 | Compose UI and layouts | Build the first task screen |
| 4 | State and interaction | Make the UI react to the user |
| 5 | Models and lists | Display several tasks |
| 6 | Add, complete, and delete | Build the working single-screen app |
| 7 | Navigation and debugging | Split the app into two screens |

---

## Important idea

Traditional beginner programs often look like this:

```text
input -> instructions -> output -> program ends
```

A mobile application behaves differently:

```text
state -> UI
  ^      |
  |      v
 events/user actions
```

The application remains active. Users generate events, events change state, and the UI updates.

This idea becomes especially clear in Part 4.

---

## Official references

- Jetpack Compose quick start: https://developer.android.com/develop/ui/compose/setup
- Android Basics with Compose: https://developer.android.com/courses/android-basics-compose/course
- Compose state: https://developer.android.com/develop/ui/compose/state
- Compose lists: https://developer.android.com/develop/ui/compose/lists
- Navigation: https://developer.android.com/guide/navigation

---

## Start

[Part 1 — Android Studio and your first app](01-android-studio-first-app.md)
