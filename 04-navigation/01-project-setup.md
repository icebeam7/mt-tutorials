# Part 1 — Project Setup

[Previous: Navigation tutorial](README.md) | [Next: Home Screen](02-home-screen.md)

## 1. Create a new project

In Android Studio:

1. File > New Project
2. Empty Activity
3. Name: `StudyPlanner`
4. Package name: `com.example.studyplanner`
5. Language: Kotlin
6. Minimum SDK: API 24+
7. Synchronize Gradle.

---

## 2. Add the navigation dependency

Open `app/build.gradle.kts` (Module :app) and add the `androidx.navigation:navigation-compose` dependency. Check the following code in the `dependencies` section:

```kotlin
dependencies {
    //...
    implementation("androidx.navigation:navigation-compose:2.10.1")
    //...
}
```

Sync the project.

---

## 3. Model the task

Create a class named `Task.kt` in the `com.example.studyplanner` package. It is a `data` class that includes an enumeration.

```kotlin
package com.example.studyplanner

enum class Priority {
    LOW,
    MEDIUM,
    HIGH
}

data class Task(
    val id: Int,
    val title: String,
    val isCompleted: Boolean = false,
    val priority: Priority = Priority.MEDIUM
)
```

---

[Previous: Navigation tutorial](README.md) | [Next: Home Screen](02-home-screen.md)
