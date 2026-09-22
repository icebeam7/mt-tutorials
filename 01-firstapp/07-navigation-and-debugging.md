# Part 7 — Navigation, debugging, and next steps

[Previous: Working task app](06-working-task-app.md) | [Tutorial home](README.md)

## Goal

Introduce Navigation Compose, and finish with essential debugging skills.

---

# Section A — Navigation

## 1. Add Navigation Compose

Open:

```text
app/build.gradle.kts
```

Inside `dependencies`, add:

```kotlin
implementation("androidx.navigation:navigation-compose:2.10.1")
```

Then synchronize Gradle.

---

## 2. Navigation vocabulary

We need three concepts:

### `NavController`

Controls navigation.

```kotlin
val navController = rememberNavController()
```

### `NavHost`

Displays the current destination.

### Destination

A screen the user can navigate to.

For this app:

```text
home
add
```

Flow:

```text
Home Screen -> Add Task -> Add Task Screen -> Save / Back -> Home Screen
```

---

## 3. Keep shared task state above the screens

Create:

```kotlin
@Composable
fun StudentTasksApp() {
    val navController = rememberNavController()

    var tasks by remember {
        mutableStateOf(
            listOf(
                Task(1, "Prepare Kotlin exercise", priority = Priority.HIGH),
                Task(2, "Read Android documentation"),
                Task(3, "Run the app", isCompleted = true)
            )
        )
    }

    NavHost(
        navController = navController,
        startDestination = "home"
    ) {
        composable("home") {
            HomeScreen(
                tasks = tasks,
                onAddTask = {
                    navController.navigate("add")
                },
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
                    tasks = tasks.filter {
                        it.id != taskToDelete.id
                    }
                }
            )
        }

        composable("add") {
            AddTaskScreen(
                onSave = { title ->
                    val task = Task(
                        id = (tasks.maxOfOrNull { it.id } ?: 0) + 1,
                        title = title
                    )

                    tasks = tasks + task
                    navController.popBackStack()
                },
                onCancel = {
                    navController.popBackStack()
                }
            )
        }
    }
}
```

The important architectural idea is:

```text
StudentTasksApp owns shared state for both HomeScreen and AddTaskScreen
```

The screens receive data and events.

---

## 4. Create `HomeScreen`

```kotlin
@Composable
fun HomeScreen(
    tasks: List<Task>,
    onAddTask: () -> Unit,
    onCompletedChange: (Task, Boolean) -> Unit,
    onDelete: (Task) -> Unit
) {
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

        Button(
            onClick = onAddTask
        ) {
            Text("Add Task")
        }

        if (tasks.isEmpty()) {
            Text("No tasks yet.")
        } else {
            TaskList(
                tasks = tasks,
                onCompletedChange = onCompletedChange,
                onDelete = onDelete,
                modifier = Modifier.weight(1f)
            )
        }
    }
}
```

Notice that `HomeScreen` does not call the `NavController`.

It simply reports:

```kotlin
onAddTask()
```

This keeps navigation concerns outside the screen.

---

## 5. Create `AddTaskScreen`

```kotlin
@Composable
fun AddTaskScreen(
    onSave: (String) -> Unit,
    onCancel: () -> Unit
) {
    var title by rememberSaveable {
        mutableStateOf("")
    }

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(24.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        Text(
            text = "Add Task",
            style = MaterialTheme.typography.headlineLarge
        )

        OutlinedTextField(
            value = title,
            onValueChange = { newValue ->
                title = newValue
            },
            label = {
                Text("Task title")
            },
            modifier = Modifier.fillMaxWidth()
        )

        Row(
            horizontalArrangement = Arrangement.spacedBy(8.dp)
        ) {
            Button(
                onClick = {
                    onSave(title.trim())
                },
                enabled = title.isNotBlank()
            ) {
                Text("Save")
            }

            OutlinedButton(
                onClick = onCancel
            ) {
                Text("Cancel")
            }
        }
    }
}
```

---

## 6. Navigation imports

You will need:

```kotlin
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable
import androidx.navigation.compose.rememberNavController
```

Android Studio can insert these automatically.

---

## Challenge 1 — Add an About screen

Add a destination:

```text
about
```

Display:

```text
Student Tasks
Introductory Kotlin/Android application
```

Add a button on Home that navigates to it.

<details>
<summary>Show solution</summary>

Add to the `NavHost`:

```kotlin
composable("about") {
    Column(
        modifier = Modifier.padding(24.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        Text(
            text = "Student Tasks",
            style = MaterialTheme.typography.headlineLarge
        )

        Text(
            "Introductory Kotlin/Android application"
        )

        Button(
            onClick = {
                navController.popBackStack()
            }
        ) {
            Text("Back")
        }
    }
}
```

Pass an About event into `HomeScreen`:

```kotlin
onAbout = {
    navController.navigate("about")
}
```

Then add an About button inside `HomeScreen`.

</details>

---

# Section B — Basic debugging

## 7. Logcat

Logcat shows messages generated by your application and Android.

Try:

```kotlin
import android.util.Log
```

Then:

```kotlin
Log.d(
    "StudentTasks",
    "Adding task: $title"
)
```

Run the app and search Logcat for:

```text
StudentTasks
```

Do not rely on logging alone for debugging, but it is extremely useful for observing execution.

---

## 8. Breakpoints

Click beside a line number in Android Studio to create a breakpoint.

For example, put one inside:

```kotlin
onSave = { title ->
    // breakpoint here
}
```

Run using **Debug** instead of Run.

When execution pauses, inspect:

- `title`,
- `tasks`,
- the current task count.

---

## 9. Read errors from the top-level cause

Beginner Android errors can look intimidating because stack traces are long.

A useful process:

1. Identify whether the problem is a compile error or runtime crash.
2. Look for your own file name.
3. Look for the first meaningful error message.
4. Inspect the referenced line.
5. Reproduce the problem.
6. Change one thing at a time.

---

## Challenge 2 — Add debug logging

Write a log message whenever a task is deleted.

<details>
<summary>Show solution</summary>

Inside the delete callback:

```kotlin
onDelete = { taskToDelete ->
    Log.d(
        "StudentTasks",
        "Deleting task ${taskToDelete.id}: ${taskToDelete.title}"
    )

    tasks = tasks.filter {
        it.id != taskToDelete.id
    }
}
```

</details>

---

## Challenge 3 — Investigate configuration change behavior

1. Type text into the Add Task screen.
2. Rotate the emulator.
3. Observe whether the typed text remains.

Why?

<details>
<summary>Show explanation</summary>

The text is stored with:

```kotlin
rememberSaveable
```

rather than only:

```kotlin
remember
```

`rememberSaveable` can preserve saveable UI state across Activity recreation, including common configuration changes such as rotation.

</details>

---

[Previous: Working task app](06-working-task-app.md) | [Tutorial home](README.md)
