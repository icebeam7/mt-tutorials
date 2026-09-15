# Part 2 — Kotlin essentials for our Android app

[Previous: Android Studio](01-android-studio-first-app.md) | [Next: Compose UI and layouts](03-compose-ui-layouts.md)

## Goal

Review the Kotlin features we need to build the app.

---

## 1. `val` and `var`

Kotlin distinguishes between values that cannot be reassigned and variables that can.

```kotlin
val courseName = "Mobile Development"
var completedTasks = 0
```

`val` means the reference cannot be reassigned:

```kotlin
val maxTasks = 10
// maxTasks = 20  // Error
```

Use `val` by default and `var` when reassignment is actually necessary.

---

## 2. Type inference

Kotlin can usually infer a type:

```kotlin
val studentName = "Ana"
val taskCount = 3
val isCompleted = false
```

You can also write the type explicitly:

```kotlin
val studentName: String = "Ana"
val taskCount: Int = 3
val isCompleted: Boolean = false
```

---

## 3. Functions

```kotlin
fun taskMessage(taskCount: Int): String {
    return "You have $taskCount tasks"
}
```

A shorter expression-body form is:

```kotlin
fun taskMessage(taskCount: Int): String =
    "You have $taskCount tasks"
```

String templates use `$`:

```kotlin
val count = 5
val message = "You have $count tasks"
```

---

## 4. Conditions

```kotlin
fun statusMessage(completed: Boolean): String {
    return if (completed) {
        "Completed"
    } else {
        "Pending"
    }
}
```

In Kotlin, `if` can return a value:

```kotlin
val status = if (completed) "Completed" else "Pending"
```

---

## 5. `when`

`when` is Kotlin's powerful alternative to many `switch` statements.

```kotlin
enum class Priority {
    LOW,
    MEDIUM,
    HIGH
}

fun priorityLabel(priority: Priority): String {
    return when (priority) {
        Priority.LOW -> "Low"
        Priority.MEDIUM -> "Medium"
        Priority.HIGH -> "High"
    }
}
```

---

## 6. Data classes

Our app needs to represent a task.

Create a new Kotlin class named:

```text
Task.kt
```

Add:

```kotlin
package com.example.studenttasks

data class Task(
    val id: Int,
    val title: String,
    val isCompleted: Boolean = false
)
```

A `data class` is very useful for data-oriented objects.

For example:

```kotlin
val task = Task(
    id = 1,
    title = "Read Kotlin documentation"
)
```

Kotlin automatically gives a data class useful functionality such as `toString`, equality, and `copy`.

Example:

```kotlin
val completedTask = task.copy(isCompleted = true)
```

The original object does not need to be mutated.

---

## 7. Lists

An immutable list:

```kotlin
val tasks = listOf(
    Task(1, "Prepare exercise"),
    Task(2, "Read documentation"),
    Task(3, "Run Android app")
)
```

Loop over it:

```kotlin
for (task in tasks) {
    println(task.title)
}
```

Or:

```kotlin
tasks.forEach { task ->
    println(task.title)
}
```

---

## 8. Lambdas

Android and Compose use lambdas frequently.

This:

```kotlin
tasks.forEach { task ->
    println(task.title)
}
```

passes a function as an argument.

You will soon see the same idea in a button:

```kotlin
Button(
    onClick = {
        // This code executes when the user taps the button.
    }
) {
    Text("Add Task")
}
```

---

## Challenge 1 — Add priority

Modify `Task` so every task has a priority.

Create an enum with:

- `LOW`
- `MEDIUM`
- `HIGH`

Make the default priority `MEDIUM`.

<details>
<summary>Show solution</summary>

```kotlin
package com.example.studenttasks

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

</details>

---

## Challenge 2 — Create a description function

Create this function:

```kotlin
fun describeTask(task: Task): String
```

It should return something similar to:

```text
Prepare exercise - HIGH
```

<details>
<summary>Show solution</summary>

```kotlin
fun describeTask(task: Task): String {
    return "${task.title} - ${task.priority}"
}
```

A shorter alternative:

```kotlin
fun describeTask(task: Task): String =
    "${task.title} - ${task.priority}"
```

</details>

---

[Previous: Android Studio](01-android-studio-first-app.md) | [Next: Compose UI and layouts](03-compose-ui-layouts.md)
