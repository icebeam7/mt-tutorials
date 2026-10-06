# Part 2 — Home Screen

[Previous: Project Setup](01-project-setup.md) | [Next: Detail Screen](03-detail-screen.md)

## 1. Create the home screen main composable

Remember: `@Composable` means that a function describes part of the UI. Jetpack Compose will draw it on screen based on the current state and parameters.

In your `MainActivity.kt` class, add a composable like this:

```kotlin
@Composable
fun HomeScreen(
    tasks: List<Task>,
    onTaskClick: (Task) -> Unit
) {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(24.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        Text(
            text = "Study Planner",
            style = MaterialTheme.typography.headlineMedium
        )

        if (tasks.isEmpty()) {
            Text("No tasks yet")
        } else {
            tasks.forEach { task ->
                TaskRow(
                    task = task,
                    onClick = { onTaskClick(task) }
                )
            }
        }
    }
}
```

## HomeScreen purpose

This `HomeScreen` composable is the screen that shows the list of tasks and lets the user tap one to open its details.

It receives:

- `tasks: List<Task>`: the current task list
- `onTaskClick: (Task) -> Unit`: a callback that runs when a task is tapped

It creates a vertical layout with:

- a title ("Study Planner")
- a list of task cards if there are tasks or a message ("No tasks yet") if the list is empty

Each task row is clickable, and when clicked it calls `onTaskClick(task)`.

This is what we call a **reusable UI component**:

- the screen is separated from the data
- the parent screen decides what happens when a task is clicked
- the child screen only displays the UI and triggers the event

The `HomeScreen` is responsible for:
- rendering tasks
- responding to taps
- sending the selected task to the navigation logic

## 2. Create the TaskRow Composable

Below the previous code, add the following Composable function:

```kotlin
@Composable
fun TaskRow(
    task: Task,
    onClick: () -> Unit
) {
    Card(
        modifier = Modifier
            .fillMaxWidth()
            .clickable { onClick() },
        colors = CardDefaults.cardColors(
            containerColor = MaterialTheme.colorScheme.surfaceVariant
        )
    ) {
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            verticalAlignment = Alignment.CenterVertically
        ) {
            Column(modifier = Modifier.weight(1f)) {
                Text(task.title)
                Text(task.priority.name)
            }

            if (task.isCompleted) {
                Text("Done")
            } else {
                Text("Pending")
            }
        }
    }
}
```

## TaskRow purpose

`TaskRow` is a small reusable UI item for one task in the list.

It receives:

- `task: Task`: the information for that specific task
- `onClick: () -> Unit`: what happens when the row is tapped

The composable builds a clickable card with:

- the task title
- the priority
- a status label: “Done” or “Pending”

---

## Card UI

The `Card` definition makes the whole row look like a card and adds click behavior.

Then inside the card, the `Row` element arranges the content horizontally.

Inside the row, the `Column` definition takes the available space and shows task title and priority name, as well as the task status.

[Previous: Project Setup](01-project-setup.md) | [Next: Detail Screen](03-detail-screen.md)