# Part 3 — Build the UI with Jetpack Compose

[Previous: Kotlin essentials](02-kotlin-essentials.md) | [Next: State and interaction](04-state-and-interaction.md)

## Goal

Create the first useful layout for the Student Tasks application.

---

## 1. Composable functions

A Compose UI is constructed from functions marked with:

```kotlin
@Composable
```

Example:

```kotlin
@Composable
fun AppTitle() {
    Text("Student Tasks")
}
```

We can combine composables:

```kotlin
@Composable
fun StudentTasksApp() {
    Column {
        AppTitle()
        Text("Your tasks appear here")
    }
}
```

This encourages us to split the UI into understandable pieces.

---

## 2. `Column`

A `Column` places its children vertically.

```kotlin
Column {
    Text("Student Tasks")
    Text("3 tasks")
    Button(onClick = { }) {
        Text("Add Task")
    }
}
```

Conceptually:

```text
Student Tasks
3 tasks
[ Add Task ]
```

---

## 3. `Row`

A `Row` places its children horizontally.

```kotlin
Row {
    Text("Task")
    Button(onClick = { }) {
        Text("Done")
    }
}
```

Conceptually:

```text
Task    [ Done ]
```

---

## 4. `Modifier`

`Modifier` changes how a composable is laid out, displayed, or interacted with.

Common examples:

```kotlin
Modifier.fillMaxWidth()
Modifier.padding(16.dp)
Modifier.weight(1f)
```

Modifiers can be chained:

```kotlin
Modifier
    .fillMaxWidth()
    .padding(16.dp)
```

Order can matter.

---

## 5. Build the first screen

Replace your `StudentTasksApp()` with:

```kotlin
@Composable
fun StudentTasksApp(modifier: Modifier = Modifier) {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(24.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        Text(
            text = "Student Tasks",
            style = MaterialTheme.typography.headlineMedium
        )

        Text("3 tasks")

        Button(
            onClick = { }
        ) {
            Text("Add Task")
        }
    }
}
```

Android Studio can automatically import most missing types with **Alt+Enter** / **Option+Enter**.

Common imports include:

```kotlin
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.material3.Button
import androidx.compose.material3.MaterialTheme
import androidx.compose.ui.unit.dp

import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.Row
import androidx.compose.ui.Alignment
import androidx.compose.material3.Checkbox

```

---

## 6. Create a reusable task row

Add:

```kotlin
@Composable
fun TaskRow(
    title: String,
    isCompleted: Boolean
) {
    Row(
        modifier = Modifier.fillMaxWidth(),
        verticalAlignment = Alignment.CenterVertically
    ) {
        Checkbox(
            checked = isCompleted,
            onCheckedChange = null
        )

        Text(
            text = title,
            modifier = Modifier.padding(start = 8.dp)
        )
    }
}
```

For now:

```kotlin
onCheckedChange = null
```

means the checkbox is displayed but is not interactive.

Use it:

```kotlin
TaskRow(
    title = "Prepare Kotlin exercise",
    isCompleted = false
)

TaskRow(
    title = "Install Android Studio",
    isCompleted = true
)
```

---

## Challenge 1 — Display priority

Change `TaskRow` so it also receives:

```kotlin
priority: Priority
```

Display the priority next to the title.

Expected idea:

```text
☐ Prepare exercise   HIGH
```

<details>
<summary>Show solution</summary>

```kotlin
@Composable
fun TaskRow(
    title: String,
    isCompleted: Boolean,
    priority: Priority
) {
    Row(
        modifier = Modifier.fillMaxWidth(),
        verticalAlignment = Alignment.CenterVertically
    ) {
        Checkbox(
            checked = isCompleted,
            onCheckedChange = null
        )

        Text(
            text = title,
            modifier = Modifier
                .padding(start = 8.dp)
                .weight(1f)
        )

        Text(priority.name)
    }
}
```

Example call:

```kotlin
TaskRow(
    title = "Prepare Kotlin exercise",
    isCompleted = false,
    priority = Priority.HIGH
)
```

</details>

---

## Challenge 2 — Add spacing between task rows

Investigate `Arrangement.spacedBy()` and place several `TaskRow` composables inside another `Column`.

<details>
<summary>Show solution</summary>

```kotlin
Column(
    verticalArrangement = Arrangement.spacedBy(8.dp)
) {
    TaskRow(
        title = "Prepare Kotlin exercise",
        isCompleted = false,
        priority = Priority.HIGH
    )

    TaskRow(
        title = "Read Android documentation",
        isCompleted = false,
        priority = Priority.MEDIUM
    )

    TaskRow(
        title = "Run the app",
        isCompleted = true,
        priority = Priority.LOW
    )
}
```

</details>

---

## Challenge 3 — Improve the title

Make the title use:

```kotlin
MaterialTheme.typography.headlineLarge
```

<details>
<summary>Show solution</summary>

```kotlin
Text(
    text = "Student Tasks",
    style = MaterialTheme.typography.headlineLarge
)
```

</details>

---

[Previous: Kotlin essentials](02-kotlin-essentials.md) | [Next: State and interaction](04-state-and-interaction.md)