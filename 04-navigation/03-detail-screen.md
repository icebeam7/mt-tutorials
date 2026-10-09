# Part 3 — Detail Screen

[Previous: Home Screen](02-home-screen.md) | [Next: Pass Arguments](04-navigation.md)

## 1. Create the detail screen

<img width="521" height="580" alt="Screenshot 2026-10-09 at 5 09 24" src="https://github.com/user-attachments/assets/b722702d-6f7e-4348-8ec6-9515173e235a" />

A detail screen receives data and shows it, so the user can see the task and update it directly.

1. Create the composable function for a new screen. This screen receives the selected task’s details and callbacks will be included so we can modify the completion change or request navigation back.

Add this below your existing composable functions in `MainActivity.kt`:

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
}
```

--- 

2. Create the local completion state. Add this inside `TaskDetailScreen`:

```kotlin
var completed by remember { mutableStateOf(isCompleted) }
```

* `isCompleted` supplies the initial value.
* `mutableStateOf` lets Compose observe changes
* `remember` preserves the value across recompositions while this composable remains in the composition.

---

3. Add the main `Column` layout just below after the state variable still inside `TaskDetailScreen`:

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

4. Add the screen heading inside `Column`:

```kotlin 
Text(
    text = "Task details",
    style = MaterialTheme.typography.headlineMedium
)
```

---

5. Display the task details after the heading still inside `Column`:

```kotlin 
Text("ID: $taskId")
Text("Title: $title")
Text("Completed: ${if (completed) "Yes" else "No"}")
Text("Priority: $priority")
```

The completion text uses the local `completed` state, so it reflects changes made with a toggle button.

---

6. Add the toggle button below the task details:

```kotlin 
Button(
    onClick = {
        completed = !completed
        onToggle(completed)
    }
) {
    Text(if (completed) "Mark as pending" else "Mark as completed")
}
```

Check the `onClick` code for the button. Tapping the button reverses the completion state and sends the new value to the parent through `onToggle`. The button label describes the next available action.

---

7. Add a back button below the previous toggle button. It calls `onBack` when tapped. 

```kotlin 
OutlinedButton(onClick = onBack) {
    Text("Back")
}
```

---

8. Add the missing imports:

```kotlin 
import androidx.compose.runtime.remember
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.getValue
import androidx.compose.runtime.setValue
import androidx.compose.material3.Button
import androidx.compose.material3.OutlinedButton
```

`getValue` and `setValue` support reading and updating the state using the `by` syntax.

[Previous: Home Screen](02-home-screen.md) | [Next: Pass Arguments](04-navigation.md)
