# Part 4 — Navigation

[Previous: Detail Screen](03-detail-screen.md) | [Next: Demo](05-demo.md)

## 1. Import required packages

We will import packages that allows the app to create a **navigation graph**, **pass typed data between screens**, and **safely encode route values like task titles**.

Make sure to import the following packages in `MainActivity.kt`:

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

---

## 2. Set up the navigation graph

1. Create the parent composable. `StudyPlannerApp` connects the home and detail screens. It will hold the shared task list and manage navigation between screens. Add this below your existing composable functions:

```kotlin 
@Composable
fun StudyPlannerApp() {
}
```

2. Create the **navigation controller**. A navigation controller lets the app move between screens, return to a previous screen, and manage the navigation back stack. Add this inside `StudyPlannerApp`:

```kotlin 
val navController = rememberNavController()
```

3. Create the shared task state. Add this inside `StudyPlannerApp`, after the navigation controller:

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

First, we added three sample tasks and stored the list in Compose state. Assigning an updated list to `tasks` lets the UI reflect the changes. `remember` keeps this state across recompositions; it does not permanently save the tasks.

4. Add the **navigation host**. Add this inside `StudyPlannerApp`, after the task state:

```kotlin 
NavHost(
    navController = navController,
    startDestination = "home"
) {
}
```

`NavHost` contains the app’s screen destinations. Setting `startDestination` to `"home"` makes the home screen the first destination.

5. Add the home screen destination. Add this inside `NavHost`:

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

* The `"home"` route displays `HomeScreen` with the shared task list.
* Tapping a task navigates to its detail screen, passing its information: ID, title, completion status, and priority.
* `Uri.encode` protects special characters in the title so they can be included in the route.

6. Define the detail screen destination. Add this below the home destination:

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
}
```

* The placeholders in the route describe the values this destination receives.
* Each `navArgument` specifies the corresponding Kotlin-compatible type.
* `backStackEntry` gives access to the arguments passed when navigating to this destination.

7. Read the navigation arguments. Add this inside the detail destination’s `backStackEntry ->` block.

```kotlin 
val taskId = backStackEntry.arguments?.getInt("taskId") ?: 0
val title = backStackEntry.arguments?.getString("title") ?: ""
val isCompleted = backStackEntry.arguments?.getBoolean("isCompleted") ?: false
val priority = backStackEntry.arguments?.getString("priority") ?: "MEDIUM"
```

* These lines read the arguments into variables that can be passed to `TaskDetailScreen`.
* The `?:` operator supplies a fallback value if the expression on its left is `null`.

8. Display the detail screen and connect its callbacks. 

We will pass the selected task’s information to `TaskDetailScreen` and connect its two actions:

- **Back:** Using `popBackStack()`, it will be possible to return to the previous destination.
- **Toggle:** We will use `map` to create a new task list. The matching task is copied with the new completion value, while the other tasks stay unchanged. Assigning the new list to `tasks` updates the shared state.

Add this inside the same detail destination block, after the argument variables.

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

[Previous: Detail Screen](03-detail-screen.md) | [Next: Demo](05-demo.md)
