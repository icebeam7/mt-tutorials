# Part 4 — Navigation

[Previous: Detail Screen](03-detail-screen.md) | [Next: Demo](05-demo.md)

## 1. Import required packages

We will import packages that allows the app to create a **navigation graph**, **pass typed data between screens**, and **safely encode route values like task titles**.

Make sure to import:

```kotlin
import android.net.Uri
import androidx.navigation.NavType
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable
import androidx.navigation.compose.rememberNavController
import androidx.navigation.navArgument
```

- `Uri`: It is used to encode text before putting it in the route.
- `NavType`: It tells the navigation system what kind of data is expected in each argument (integer, string, etc).
- `NavHost`: It is the container that manages the app’s screens. We will use to set the initial screen and define which screens are available. 
- `composable`: It is used to register each screen in the navigation graph.
- `rememberNavController`: It creates the navigation controller instance for the app.
- `navArgument`: It defines each argument expected by a route.

## 2. Set up the navigation graph

In `MainActivity.kt`, create a parent composable:

```kotlin
@Composable
fun StudyPlannerApp() {
    val navController = rememberNavController()

    var tasks by remember {
        mutableStateOf(
            listOf(
                Task(1, "Study for my exam", false, Priority.HIGH),
                Task(2, "Buy groceries", true, Priority.MEDIUM),
                Task(3, "Read a book", false, Priority.LOW)
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
                onTaskClick = { task ->
                    navController.navigate(
                        "taskDetail/${task.id}/${Uri.encode(task.title)}/${task.isCompleted}/${task.priority.name}"
                    )
                }
            )
        }

        composable(
            route = "taskDetail/{taskId}/{title}/{isCompleted}/{priority}",
            arguments = listOf(
                navArgument("taskId") { type = NavType.IntType },
                navArgument("title") { type = NavType.StringType },
                navArgument("isCompleted") { type = NavType.BoolType },
                navArgument("priority") { type = NavType.StringType }
            )
        ) { backStackEntry ->
            val taskId = backStackEntry.arguments?.getInt("taskId") ?: 0
            val title = backStackEntry.arguments?.getString("title") ?: ""
            val isCompleted = backStackEntry.arguments?.getBoolean("isCompleted") ?: false
            val priority = backStackEntry.arguments?.getString("priority") ?: "MEDIUM"

            TaskDetailScreen(
                taskId = taskId,
                title = title,
                isCompleted = isCompleted,
                priority = priority,
                onBack = { navController.popBackStack() },
                onToggle = { newValue ->
                    tasks = tasks.map { task ->
                        if (task.id == taskId) {
                            task.copy(isCompleted = newValue)
                        } else {
                            task
                        }
                    }
                }
            )
        }
    }
}
```

Use the `StudyPlannerApp` in `onCreate` method.

Here's the explanation of the code:

`StudyPlannerApp` is the main screen container for the app. It creates the navigation system and holds the shared task list. It is the root composable that owns the app state, configures the navigation graph, and connects the home screen and detail screen together.

It does three important things:

1. creates a `NavController`
2. keeps the list of tasks in state
3. defines the screens and how they navigate

---

## Navigation controller

```kotlin
val navController = rememberNavController()
```

This gives the app a navigation object that can:
- move to another screen
- go back
- manage the current screen stack

---

## Shared task state

```kotlin
var tasks by remember {
    mutableStateOf(
        listOf(
            Task(1, "Study for my exam", false, Priority.HIGH),
            Task(2, "Buy groceries", true, Priority.MEDIUM),
            Task(3, "Read a book", false, Priority.LOW)
        )
    )
}
```

This creates the app’s data model for the task list.

It is stored in Compose state, which means when a task is updated, the UI refreshes automatically.

---

## Navigation graph

```kotlin
NavHost(
    navController = navController,
    startDestination = "home"
)
```

This is the root of the navigation system.

- `startDestination = "home"` means the app opens the home screen first.
- `NavHost` contains all the screens.

---

## Home screen route

```kotlin
composable("home") {
    HomeScreen(
        tasks = tasks,
        onTaskClick = { task ->
            navController.navigate(
                "taskDetail/${task.id}/${Uri.encode(task.title)}/${task.isCompleted}/${task.priority.name}"
            )
        }
    )
}
```

This says:

- when the app is on the `"home"` route, show `HomeScreen`
- pass the `tasks` list to it
- when a task is clicked, navigate to the detail route with the selected task data

The route is built like this:

```kotlin
"taskDetail/${task.id}/${Uri.encode(task.title)}/${task.isCompleted}/${task.priority.name}"
```

This includes:
- task ID
- title
- completion status
- priority

The title is encoded with `Uri.encode(...)` so spaces and special characters are safe in the URL route.

---

## Detail screen route

```kotlin
composable(
    route = "taskDetail/{taskId}/{title}/{isCompleted}/{priority}",
    arguments = listOf(
        navArgument("taskId") { type = NavType.IntType },
        navArgument("title") { type = NavType.StringType },
        navArgument("isCompleted") { type = NavType.BoolType },
        navArgument("priority") { type = NavType.StringType }
    )
) { backStackEntry ->
```

This defines the detail screen route and tells Compose what type each argument is.

The app passes:
- `Int` for the task ID,
- `String` for the task title,
- `Boolean` for completion status,
- `String` for the priority.

Navigation is not only moving between screens; it is also transferring data.

Then it reads them:

```kotlin
val taskId = backStackEntry.arguments?.getInt("taskId") ?: 0
val title = backStackEntry.arguments?.getString("title") ?: ""
val isCompleted = backStackEntry.arguments?.getBoolean("isCompleted") ?: false
val priority = backStackEntry.arguments?.getString("priority") ?: "MEDIUM"
```

This converts the route values back into real Kotlin variables.

Then it displays the detail screen:

```kotlin
TaskDetailScreen(
    taskId = taskId,
    title = title,
    isCompleted = isCompleted,
    priority = priority,
    onBack = { navController.popBackStack() },
    onToggle = { newValue ->
        tasks = tasks.map { task ->
            if (task.id == taskId) {
                task.copy(isCompleted = newValue)
            } else {
                task
            }
        }
    }
)
```

This is the main idea:

- when the user taps a task, the app navigates with its data
- the detail screen reads that data
- when the task is toggled, the parent updates the shared `tasks` list

---

## 3. Call the parent composable

In `onCreate` set the `StudyPlannerApp` composable as the initial UI:

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContent {
            StudyPlannerTheme {
                StudyPlannerApp()
            }
        }
    }
}
```

[Previous: Detail Screen](03-detail-screen.md) | [Next: Demo](05-demo.md)
