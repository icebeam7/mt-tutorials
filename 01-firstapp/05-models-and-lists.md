# Part 5 — Display task models in a list

[Previous: State and interaction](04-state-and-interaction.md) | [Next: Add, complete, delete](06-working-task-app.md)

## Goal

Represent application data as `Task` objects and display them efficiently using `LazyColumn`.

---

## 1. Confirm the model

Your `Task.kt` should look similar to:

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

---

## 2. Create sample data

Inside the app composable:

```kotlin
val sampleTasks = listOf(
    Task(
        id = 1,
        title = "Prepare Kotlin exercise",
        priority = Priority.HIGH
    ),
    Task(
        id = 2,
        title = "Read Android documentation",
        priority = Priority.MEDIUM
    ),
    Task(
        id = 3,
        title = "Run the app",
        isCompleted = true,
        priority = Priority.LOW
    )
)
```

---

## 3. Why `LazyColumn`?

A regular `Column` is useful for a small fixed number of children.

For collections that may grow or scroll, Compose provides `LazyColumn`.

It composes list content as needed rather than treating every possible row as permanently visible.

---

## 4. Display the tasks

Create:

```kotlin
@Composable
fun TaskList(
    tasks: List<Task>
) {
    LazyColumn(
        verticalArrangement = Arrangement.spacedBy(8.dp)
    ) {
        items(
            items = tasks,
            key = { task -> task.id }
        ) { task ->
            TaskRow(task)
        }
    }
}
```

Then create:

```kotlin
@Composable
fun TaskRow(task: Task) {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .padding(vertical = 4.dp),
        verticalAlignment = Alignment.CenterVertically
    ) {
        Checkbox(
            checked = task.isCompleted,
            onCheckedChange = null
        )

        Column(
            modifier = Modifier
                .padding(start = 8.dp)
                .weight(1f)
        ) {
            Text(task.title)
            Text(
                text = task.priority.name,
                style = MaterialTheme.typography.bodySmall
            )
        }
    }
}
```

Now display:

```kotlin
TaskList(sampleTasks)
```

---

## 5. Show the count dynamically

Instead of:

```kotlin
Text("3 tasks")
```

use:

```kotlin
Text("${sampleTasks.size} tasks")
```

Or you can also use:

```kotlin
val completedCount = sampleTasks.count { it.isCompleted }

Text(
    "$completedCount of ${sampleTasks.size} completed"
)
```

This is calculated from the actual data.

---

## Challenge 1 — Empty state

If the list contains no tasks, display:

```text
No tasks yet.
```

Otherwise display the `LazyColumn`.

<details>
<summary>Show solution</summary>

```kotlin
if (tasks.isEmpty()) {
    Text("No tasks yet.")
} else {
    TaskList(tasks)
}
```

</details>

---

## Challenge 2 — Completed style

Display completed task titles with a strikethrough.

Investigate:

```kotlin
TextDecoration.LineThrough
```

<details>
<summary>Show solution</summary>

Add:

```kotlin
import androidx.compose.ui.text.style.TextDecoration
```

Then:

```kotlin
Text(
    text = task.title,
    textDecoration = if (task.isCompleted) {
        TextDecoration.LineThrough
    } else {
        null
    }
)
```

</details>

---

## Challenge 3 — Sort by priority

Try displaying high-priority tasks before medium and low priority tasks.

One simple approach is to assign an order with `when`.

<details>
<summary>Show solution</summary>

```kotlin
val sortedTasks = tasks.sortedBy { task ->
    when (task.priority) {
        Priority.HIGH -> 0
        Priority.MEDIUM -> 1
        Priority.LOW -> 2
    }
}
```

Then:

```kotlin
TaskList(sortedTasks)
```

</details>

---

## Challenge 4 — Filter completed tasks

Create:

```kotlin
val pendingTasks = ...
```

containing only tasks that are not completed.

<details>
<summary>Show solution</summary>

```kotlin
val pendingTasks = tasks.filter { task ->
    !task.isCompleted
}
```

Or:

```kotlin
val pendingTasks = tasks.filter { !it.isCompleted }
```

</details>

---

Your UI now uses actual application models:

```text
    List<Task> > LazyColumn > TaskRow(Task)
```

The next step makes the checkboxes and buttons modify the list.

---

[Previous: State and interaction](04-state-and-interaction.md) | [Next: Add, complete, delete](06-working-task-app.md)
