# 02 — Move logic into the ViewModel

For beginner apps, it can feel easy to put all the logic inside `MainActivity` or a composable. But that becomes complicated quickly. The UI and app can work, but the logic is usually spread across the screen. In a real app, we want a cleaner pattern.

## The idea

The screen should not directly decide how the counter changes. Instead, it should call methods on the `ViewModel`:

- `increment()`
- `decrement()`
- `reset()`

This is the main separation of concerns.

## Create the ViewModel file

Create a new file named `CounterViewModel.kt`.

```kotlin
package com.example.counterhistoryapp

import androidx.lifecycle.ViewModel
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.setValue
import androidx.compose.runtime.mutableStateListOf

class CounterViewModel : ViewModel() {
    var count by mutableStateOf(0)
        private set

    fun increment() {
        count++
    }

    fun decrement() {
        count--
    }

    fun reset() {
        count = 0
    }
}
```

### Purpose

This class is the brain of the screen. 

This class stores the app state (counter value and history list) and exposes methods that change it intentionally. 

### Explanation

- `mutableStateOf(0)` creates observable state for Compose.
- `private set` prevents direct mutation from outside the class (the UI cannot directly change `count` from outside the ViewModel).
- `count` is the current value.
- `history` is a mutable list that keeps track of each count update the user reached.
- Every method updates state in one place:
    - `increment()` and `decrement()` update the state.
    - `reset()` clears both the count and the history list.

This is exactly what a `ViewModel` is good for: centralizing screen state and logic.

This is the first important idea: the UI should ask the `ViewModel` to change state, not modify data directly.

---

## Why this is better than putting logic in the composable

When logic is inside the screen:

- it becomes harder to read
- it mixes UI code with app behavior
- it is harder to test
- it is harder to maintain

With a `ViewModel`:

- the UI is focused on rendering
- the logic is focused on state updates
- the app becomes easier to extend

This is the most important conceptual step of the whole tutorial.

---

## Important rule

A good screen usually does this:

```kotlin
Button(onClick = { viewModel.increment() }) {
    Text("+")
}
```

It should not do this:

```kotlin
Button(onClick = { count++ }) {
    Text("+")
}
```

Why? Because `count++` is not the same as a proper state structure for a screen with more than one responsibility. The `ViewModel` gives us a clean place to store and update state.

---

## What will change in the app architecture?

Before:

```text
Activity / Composable
    -> directly controls state
```

After:

```text
Composable
    -> calls ViewModel methods
ViewModel
    -> holds and updates state
```

This simple change is the foundation of modern Android architecture.

## Next step

In the next file, we will build the actual counter UI and connect it to this ViewModel.

[Next: 03 — Build the counter UI](03-counter-screen.md)
