# Part 4 — State and user interaction

[Previous: Compose UI](03-compose-ui-layouts.md) | [Next: Models and lists](05-models-and-lists.md)

## Goal

Understand one of the most important concepts in Compose:

> **When application state changes, Compose can update the UI that reads that state.**

---

## 1. Start with a counter

Temporarily use this small example:

```kotlin
@Composable
fun CounterExample() {
    var count by remember { mutableStateOf(0) }

    Column {
        Text("Tasks: $count")

        Button(
            onClick = {
                count++
            }
        ) {
            Text("Add")
        }
    }
}
```

You may need:

```kotlin
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
import androidx.compose.runtime.setValue
```

Replace this line in `StudentTasksApp`:

```kotlin
Text("3 tasks")
```

with 

```kotlin
CounterExample()
```


---

## 2. What is state?

Here:

```kotlin
var count by remember { mutableStateOf(0) }
```

`count` is state observed by Compose.

When this executes:

```kotlin
count++
```

the state changes.

Because the `Text` reads `count`:

```kotlin
Text("Tasks: $count")
```

Compose can recompose the relevant UI.

Think of it as:

* `count = 0` means `Tasks: 0`
* when the user taps button, `count = 1` and updates to `Tasks: 1`

---

## 3. What does `remember` do?

`remember` keeps a value across recompositions while that composable remains in the composition.

Without it, a local value could simply be recreated when Compose reruns the function.

For state that should survive Activity recreation, Compose also provides `rememberSaveable`.

For simple text input in this tutorial, we will use `rememberSaveable`.

---

## 4. Read text from the user

Create:

```kotlin
@Composable
fun AddTaskExample() {
    var taskTitle by rememberSaveable {
        mutableStateOf("")
    }

    Column {
        OutlinedTextField(
            value = taskTitle,
            onValueChange = { newValue ->
                taskTitle = newValue
            },
            label = {
                Text("Task title")
            }
        )

        Text("You typed: $taskTitle")
    }
}
```

You may need:

```kotlin
import androidx.compose.material3.OutlinedTextField
import androidx.compose.runtime.saveable.rememberSaveable
```

Replace these lines in `StudentTasksApp`:

```kotlin
        Button(
            onClick = { }
        ) {
            Text("Add Task")
        }
```

with 

```kotlin
fun AddTaskExample() {
CounterExample()
```

Important relationship:

* `TextField` displays state 
* we use the variable `taskTitle` for state here
* `onValueChange` modifies state

This is a simple form of **unidirectional data flow**.

---

## 5. Events are lambdas

The button:

```kotlin
Button(
    onClick = {
        println("Button clicked")
    }
) {
    Text("Add Task")
}
```

receives a lambda for `onClick`.

Similarly:

```kotlin
OutlinedTextField(
    value = taskTitle,
    onValueChange = { newValue ->
        taskTitle = newValue
    }
)
```

receives a lambda that reacts to text changes.

---

## Challenge 1 — Clear the text

Add a button named:

```text
Clear
```

When clicked, it should set `taskTitle` to an empty string.

<details>
<summary>Show solution</summary>

```kotlin
Button(
    onClick = {
        taskTitle = ""
    }
) {
    Text("Clear")
}
```

</details>

---

## Challenge 2 — Disable Add when empty

Create:

```kotlin
Button(...)
```

whose `enabled` value is `false` when the text field is blank.

Hint:

```kotlin
taskTitle.isNotBlank()
```

<details>
<summary>Show solution</summary>

```kotlin
Button(
    onClick = {
        println("Add $taskTitle")
    },
    enabled = taskTitle.isNotBlank()
) {
    Text("Add Task")
}
```

</details>

---

## Challenge 3 — Count button presses

Add another state variable called:

```kotlin
addAttempts
```

Increase it every time an enabled Add button is clicked and show:

```text
Tasks added: 3
```

<details>
<summary>Show solution</summary>

```kotlin
var addAttempts by remember {
    mutableStateOf(0)
}
```

Then:

```kotlin
Button(
    onClick = {
        addAttempts++
    },
    enabled = taskTitle.isNotBlank()
) {
    Text("Add Task")
}

Text("Tasks added: $addAttempts")
```

</details>

---

You should now understand the basic cycle:

```text
State > UI > User event > Change state > Recomposition
```

Next we use that cycle with real `Task` objects.

---

[Previous: Compose UI](03-compose-ui-layouts.md) | [Next: Models and lists](05-models-and-lists.md)
