# Part 6 — Build the working task app

[Previous: Models and lists](05-models-and-lists.md) | [Next: Navigation and debugging](07-navigation-and-debugging.md)

## Goal

Create a working single-screen application where users can:

- add tasks,
- complete/uncomplete tasks,
- delete tasks.

---

## 1. Keep the tasks in observable state

Inside `StudentTasksApp()`:

```kotlin
var tasks by remember {
    mutableStateOf(
        listOf(
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
    )
}
```

Also create state for the new task title:

```kotlin
var newTaskTitle by rememberSaveable {
    mutableStateOf("")
}
```

---

## 2. Add a task

Create the input:

```kotlin
OutlinedTextField(
    value = newTaskTitle,
    onValueChange = { newValue ->
        newTaskTitle = newValue
    },
    label = {
        Text("New task")
    },
    modifier = Modifier.fillMaxWidth()
)
```

Add the button:

```kotlin
Button(
    onClick = {
        val newTask = Task(
            id = (tasks.maxOfOrNull { it.id } ?: 0) + 1,
            title = newTaskTitle.trim()
        )

        tasks = tasks + newTask
        newTaskTitle = ""
    },
    enabled = newTaskTitle.isNotBlank()
) {
    Text("Add Task")
}
```

Notice:

```kotlin
tasks = tasks + newTask
```

We create a new list and replace the old state value.

---

## 3. Send events out of `TaskRow`

A reusable UI component should not necessarily own the whole application state.

Define:

```kotlin
@Composable
fun TaskRow(
    task: Task,
    onCompletedChange: (Boolean) -> Unit,
    onDelete: () -> Unit
) {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .padding(vertical = 4.dp),
        verticalAlignment = Alignment.CenterVertically
    ) {
        Checkbox(
            checked = task.isCompleted,
            onCheckedChange = onCompletedChange
        )

        Column(
            modifier = Modifier
                .padding(horizontal = 8.dp)
                .weight(1f)
        ) {
            Text(
                text = task.title,
                textDecoration = if (task.isCompleted) {
                    TextDecoration.LineThrough
                } else {
                    null
                }
            )

            Text(
                text = task.priority.name,
                style = MaterialTheme.typography.bodySmall
            )
        }

        OutlinedButton(
            onClick = onDelete
        ) {
            Text("Delete")
        }
    }
}
```

`TaskRow` receives events as functions. It makes this composable reusable.

---

## 4. Update the list when a checkbox changes

Create:

```kotlin
@Composable
fun TaskList(
    tasks: List<Task>,
    onCompletedChange: (Task, Boolean) -> Unit,
    onDelete: (Task) -> Unit,
    modifier: Modifier = Modifier
) {
    LazyColumn(
        modifier = modifier,
        verticalArrangement = Arrangement.spacedBy(8.dp)
    ) {
        items(
            items = tasks,
            key = { task -> task.id }
        ) { task ->
            TaskRow(
                task = task,
                onCompletedChange = { isCompleted ->
                    onCompletedChange(task, isCompleted)
                },
                onDelete = {
                    onDelete(task)
                }
            )
        }
    }
}
```

Now call it from the app:

```kotlin
TaskList(
    tasks = tasks,
    onCompletedChange = { changedTask, isCompleted ->
        tasks = tasks.map { task ->
            if (task.id == changedTask.id) {
                task.copy(isCompleted = isCompleted)
            } else {
                task
            }
        }
    },
    onDelete = { taskToDelete ->
        tasks = tasks.filter { task ->
            task.id != taskToDelete.id
        }
    }
)
```

Observe the two immutable transformations:

```kotlin
tasks.map { ... }
```

creates an updated list.

```kotlin
tasks.filter { ... }
```

creates a list without the deleted task.

---

## 5. Complete `StudentTasksApp`

Use this as the checkpoint implementation:

```kotlin
@Composable
fun StudentTasksApp() {
    var tasks by remember {
        mutableStateOf(
            listOf(
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
        )
    }

    var newTaskTitle by rememberSaveable {
        mutableStateOf("")
    }

    val completedCount = tasks.count { it.isCompleted }

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(24.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        Text(
            text = "Student Tasks",
            style = MaterialTheme.typography.headlineLarge
        )

        Text(
            text = "$completedCount of ${tasks.size} completed"
        )

        OutlinedTextField(
            value = newTaskTitle,
            onValueChange = { newValue ->
                newTaskTitle = newValue
            },
            label = {
                Text("New task")
            },
            modifier = Modifier.fillMaxWidth()
        )

        Button(
            onClick = {
                val newTask = Task(
                    id = (tasks.maxOfOrNull { it.id } ?: 0) + 1,
                    title = newTaskTitle.trim()
                )

                tasks = tasks + newTask
                newTaskTitle = ""
            },
            enabled = newTaskTitle.isNotBlank()
        ) {
            Text("Add Task")
        }

        if (tasks.isEmpty()) {
            Text("No tasks yet.")
        } else {
            TaskList(
                tasks = tasks,
                onCompletedChange = { changedTask, isCompleted ->
                    tasks = tasks.map { task ->
                        if (task.id == changedTask.id) {
                            task.copy(isCompleted = isCompleted)
                        } else {
                            task
                        }
                    }
                },
                onDelete = { taskToDelete ->
                    tasks = tasks.filter { task ->
                        task.id != taskToDelete.id
                    }
                },
                modifier = Modifier.weight(1f)
            )
        }
    }
}
```

---

## 6. Suggested imports

Android Studio can insert imports automatically, but your file will use items similar to:

```kotlin
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.weight
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material3.Button
import androidx.compose.material3.Checkbox
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.OutlinedButton
import androidx.compose.material3.OutlinedTextField
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
import androidx.compose.runtime.saveable.rememberSaveable
import androidx.compose.runtime.setValue
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.text.style.TextDecoration
import androidx.compose.ui.unit.dp
```

---

## Challenge 1 — Clear completed tasks

Add a button:

```text
Clear completed
```

It should remove every completed task.

<details>
<summary>Show solution</summary>

```kotlin
OutlinedButton(
    onClick = {
        tasks = tasks.filter { task ->
            !task.isCompleted
        }
    }
) {
    Text("Clear completed")
}
```

You can optionally disable it when there are no completed tasks:

```kotlin
OutlinedButton(
    onClick = {
        tasks = tasks.filter { !it.isCompleted }
    },
    enabled = tasks.any { it.isCompleted }
) {
    Text("Clear completed")
}
```

</details>

---

## Challenge 2 — Prevent duplicate task names

Do not enable the Add button if another task already has the same title, ignoring upper/lower case.

<details>
<summary>Show solution</summary>

Create:

```kotlin
val duplicateTitle = tasks.any { task ->
    task.title.equals(
        newTaskTitle.trim(),
        ignoreCase = true
    )
}
```

Then:

```kotlin
Button(
    onClick = {
        // Existing add logic
    },
    enabled = newTaskTitle.isNotBlank() && !duplicateTitle
) {
    Text("Add Task")
}
```

</details>

---

## Challenge 3 — Add a priority selector

Add state for the selected priority and let users choose Low, Medium, or High before creating a task.

Try researching one of these Compose components:

- `DropdownMenu`
- `RadioButton`

<details>
<summary>Show one possible solution using RadioButton</summary>

Create state:

```kotlin
var selectedPriority by remember {
    mutableStateOf(Priority.MEDIUM)
}
```

Display the choices:

```kotlin
Priority.entries.forEach { priority ->
    Row(
        verticalAlignment = Alignment.CenterVertically
    ) {
        RadioButton(
            selected = selectedPriority == priority,
            onClick = {
                selectedPriority = priority
            }
        )

        Text(priority.name)
    }
}
```

When creating the task:

```kotlin
val newTask = Task(
    id = (tasks.maxOfOrNull { it.id } ?: 0) + 1,
    title = newTaskTitle.trim(),
    priority = selectedPriority
)
```

</details>

---

## Challenge 4 — Show progress percentage

Display:

```text
Progress: 67%
```

Be careful when the task list is empty.

<details>
<summary>Show solution</summary>

```kotlin
val progressPercent = if (tasks.isEmpty()) {
    0
} else {
    completedCount * 100 / tasks.size
}
```

Then:

```kotlin
Text("Progress: $progressPercent%")
```

</details>

---

[Previous: Models and lists](05-models-and-lists.md) | [Next: Navigation and debugging](07-navigation-and-debugging.md)
