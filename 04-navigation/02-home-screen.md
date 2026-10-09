# Part 2 — Home Screen

[Previous: Project Setup](01-project-setup.md) | [Next: Detail Screen](03-detail-screen.md)

<img width="521" height="580" alt="Screenshot 2026-10-09 at 5 09 15" src="https://github.com/user-attachments/assets/53c8b290-29d1-4863-bbf6-3c4a3b473157" />

## 1. Create the home screen main composable

Remember: `@Composable` means that a function describes part of the UI. Jetpack Compose will draw it on screen based on the current state and parameters.

1. In your `MainActivity.kt` class, add a UI function `HomeScreen` with 2 arguments: `tasks` (the data to show) and `onTaskClick` (a callback for when a task is tapped).

```kotlin
@Composable
fun HomeScreen(
    tasks: List<Task>,
    onTaskClick: (Task) -> Unit
) {
}
```

---

2. Inside the UI function, add a `Column` layout 

```kotlin
Column(
    modifier = Modifier
        .fillMaxSize()
        .padding(24.dp),
    verticalArrangement = Arrangement.spacedBy(16.dp)
) {
}
```

---

3. The first element of the Column layout is a screen title text:

```kotlin
Text(
    text = "Study Planner",
    style = MaterialTheme.typography.headlineMedium
)
```

---

4. Next, show a message when there are no tasks (handles the empty state so users get feedback instead of a blank area):

```kotlin
if (tasks.isEmpty()) {
    Text("No tasks yet")
}
```

---

5. Add the `else` branch to render tasks. If tasks exist, loop through each one and show a `TaskRow`. Clicking a row calls `onTaskClick(task)` with that specific task:

```kotlin
else {
    tasks.forEach { task ->
        TaskRow(
            task = task,
            onClick = { onTaskClick(task) }
        )
    }
}
```

---

6. Add required imports, for example:

```kotlin
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.material3.MaterialTheme
import androidx.compose.ui.unit.dp
```

---

`TaskRow` is not defined yet, so a temporary error is normal. It will disappear after you create `TaskRow` in the next section.

The `HomeScreen` is responsible for:
- rendering tasks
- responding to taps
- sending the selected task to the navigation logic

---

## 2. Create the TaskRow Composable

1. Below the `HomeScreen` function, add a new `TaskRow` Composable function. `TaskRow` is a small reusable UI item for one task in the list, so it will display one task. `task` provides its details, and `onClick` defines what happens when the row is tapped. 

```kotlin
@Composable
fun TaskRow(
    task: Task,
    onClick: () -> Unit
) {
}
```

---

2. Add a clickable card inside `TaskRow`:

```kotlin
Card(
    modifier = Modifier
        .fillMaxWidth()
        .clickable { onClick() },
    colors = CardDefaults.cardColors(
        containerColor = MaterialTheme.colorScheme.surfaceVariant
    )
) {
}
```

---

3. Add a horizontal layout inside `Card` using `Row`: 

```kotlin
Row(
    modifier = Modifier
        .fillMaxWidth()
        .padding(16.dp),
    verticalAlignment = Alignment.CenterVertically
) {
}
```

---

4. Display the task title and priority inside `Row` using a `Column` to stack them together vertically:

```kotlin 
Column(modifier = Modifier.weight(1f)) {
    Text(task.title)
    Text(task.priority.name)
}
```

5. Display the completion status below the `Column`, still inside `Row`:

```kotlin 
if (task.isCompleted) {
    Text("Done")
} else {
    Text("Pending")
}
```

6. Add the missing imports:

```kotlin
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.material3.Card
import androidx.compose.material3.CardDefaults
import androidx.compose.ui.Alignment
```

---

## Card UI

The `Card` definition makes the whole row look like a card and adds click behavior.

Then inside the card, the `Row` element arranges the content horizontally.

Inside the row, the `Column` definition takes the available space and shows task details (title and priority), as well as the task status.

[Previous: Project Setup](01-project-setup.md) | [Next: Detail Screen](03-detail-screen.md)
