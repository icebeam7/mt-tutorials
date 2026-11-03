# 04 — Add the history tracker

Now we add the list that records every value reached by the counter.

## What the user should see

A list beneath the counter should look like this:

- 1
- 2
- 3
- 4

The list updates whenever the user presses `+` or `-`.

## Update the ViewModel

Update the code of the `CounterViewModel` class:

```kotlin
class CounterViewModel : ViewModel() {
    var count by mutableStateOf(0)
        private set

    val history = mutableStateListOf<Int>()

    fun increment() {
        count++
        history.add(count)
    }

    fun decrement() {
        count--
        history.add(count)
    }

    fun reset() {
        count = 0
        history.clear()
    }
}
```

The history list keeps every value that appeared during the session.

### Explanation

- `history` is a list of integers.
- `history.add(count)` stores the current count after each modification.
- `history.clear()` removes every item when reset is pressed.

The list is simple but still meaningful.

---

## Update UI with the history list

Now, update the `CounterHistoryApp` composable in `MainActivity`:

```kotlin
@Composable
fun CounterHistoryApp(
    viewModel: CounterViewModel = viewModel()
) {
    val count = viewModel.count
    val history = viewModel.history

    Surface(
        modifier = Modifier.fillMaxSize(),
        color = MaterialTheme.colorScheme.background
    ) {
        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(24.dp),
            verticalArrangement = Arrangement.Center,
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            Text(
                text = count.toString(),
                style = MaterialTheme.typography.displayLarge
            )

            Row(
                horizontalArrangement = Arrangement.spacedBy(16.dp),
                modifier = Modifier.padding(top = 24.dp)
            ) {
                Button(onClick = { viewModel.decrement() }) {
                    Text("-")
                }

                Button(onClick = { viewModel.reset() }) {
                    Text("Reset")
                }

                Button(onClick = { viewModel.increment() }) {
                    Text("+")
                }
            }

            Text(
                text = "History",
                style = MaterialTheme.typography.titleMedium,
                modifier = Modifier.padding(top = 32.dp, bottom = 8.dp)
            )

            history.forEach { value ->
                Text(text = value.toString())
            }
        }
    }
}
```

This displays the full set of previously visited values.

### Explanation

- `val history = viewModel.history` reads the state from the ViewModel.
- `history.forEach { value -> Text(value.toString()) }` prints each item in the list.
- When the history list changes, Compose automatically redraws the screen.

This is a simple but powerful example of observable state in Compose.

---

The history list is a classic case where data is not just a single number. It is a collection of values that changes over time.

If we kept that list in the composable without a `ViewModel`, then:

- the state could disappear during rotation
- the logic would be harder to track
- the UI would become harder to maintain

The `ViewModel` gives us a stable place to keep this state.

---

The app follows this pattern:

```text
user action -> ViewModel method -> state update -> Compose recomposition
```

In other words:

```text
UI receives events -> ViewModel updates state -> UI redraws
```

- the screen observes data from `ViewModel`
- the screen sends user actions to the `ViewModel`
- the `ViewModel` updates state
- Compose re-renders the screen automatically

This is the core flow of UI state in Jetpack Compose.

The ViewModel class is the center of the app logic. It is where the app state lives. The UI is only a shell that displays it and sends user input into it.

## Key ideas to remember

- A `ViewModel` is a state holder for the screen.
- It survives configuration changes like rotation.
- The UI should not own all state logic.
- Compose reacts to state updates automatically.
- A screen should call methods like `increment()` instead of manipulating data directly.
