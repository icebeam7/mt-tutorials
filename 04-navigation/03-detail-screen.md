# Part 3 — Detail Screen

[Previous: Home Screen](02-home-screen.md) | [Next: Pass Arguments](04-navigation.md)

## 1. Create the detail screen

A detail screen receives data and shows it, so the user can see the task and update it directly.

Add the following composable in `MainActivity.kt`:

```kotlin
@Composable
fun TaskDetailScreen(
    taskId: Int,
    title: String,
    isCompleted: Boolean,
    priority: String,
    onBack: () -> Unit,
    onToggle: (Boolean) -> Unit
) {
    var completed by remember { mutableStateOf(isCompleted) }

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(24.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        Text(
            text = "Task details",
            style = MaterialTheme.typography.headlineMedium
        )

        Text("ID: $taskId")
        Text("Title: $title")
        Text("Completed: ${if (completed) "Yes" else "No"}")
        Text("Priority: $priority")

        Button(
            onClick = {
                completed = !completed
                onToggle(completed)
            }
        ) {
            Text(if (completed) "Mark as pending" else "Mark as completed")
        }

        OutlinedButton(onClick = onBack) {
            Text("Back")
        }
    }
}
```

## TaskDetailScreen purpose

`TaskDetailScreen` is the screen that shows the selected task in more detail.

It receives several arguments:

- `taskId: Int`
- `title: String`
- `isCompleted: Boolean`
- `priority: String`
- `onBack: () -> Unit`
- `onToggle: (Boolean) -> Unit`

This means that this screen is not holding the whole app state. It just receives the values it needs and reports changes back to the parent.

Once the information about one task is displayed, the user can:

- see the task details
- toggle its completion state
- go back to the previous screen

## State

```kotlin
var completed by remember { mutableStateOf(isCompleted) }
```

Above code is really important! It creates a local state variable named `completed`.

- An incoming `isCompleted` is just the initial value from navigation.
- The screen may change it while the user taps the button.
- `remember` keeps this value while the screen stays on the screen.

So the UI updates immediately without reloading the whole app.

## Task details

The task details are represented with 4 `Text` elements inside a `Column` layout for the task ID, task title, whether it is completed, and its priority.

The screen reflects the latest value after clicking the button (`isCompleted`).

## Toggle button

Moreover, the `Button` element has a toggle behavior. When the user taps the button:

1. the local state flips
2. the UI updates
3. the new value is sent to `onToggle`

This callback then updates the task list in the parent screen.

## Back button

The `OutlinedButton` element calls `onBack` which will take the user back to the previous screen.

[Previous: Home Screen](02-home-screen.md) | [Next: Pass Arguments](04-navigation.md)