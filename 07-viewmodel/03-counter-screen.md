# 02 — Build the counter screen

Now we build the visible UI: the number, buttons, and the reset action.

## The composable we want

We want a screen that displays:

- the current number
- a `-` button
- a `+` button
- a `Reset` button

We will use a `viewModel()` instance inside the composable.

## Add a dependency

Add the following dependency in `build.gradle.kts (Module :app)`.

```
implementation "androidx.lifecycle:lifecycle-viewmodel-compose:2.4.1"
```

Don't forget to sync the project.

## Code: the screen UI

### Step 1: Import the packages

First, we import the **Compose** components and the **ViewModel APIs** we need to build the screen and manage state.

```kotlin
package com.example.counterhistoryapp

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.Button
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Surface
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import androidx.lifecycle.viewmodel.compose.viewModel
import com.example.counterhistoryapp.viewmodels.CounterViewModel
```

### Step 2: Create the Composable Screen

2.1 Create a Composable screen named `CounterHistoryApp`:

```kotlin
@Composable
fun CounterHistoryApp(
) {

}
```

2.2 This screen receives a ViewModel instance, so adapt the Composable accordingly:

```kotlin
@Composable
fun CounterHistoryApp(
    viewModel: CounterViewModel = viewModel()
) {

}
```

We are assigning a default parameter. If no value is passed in, Compose automatically creates one instance for you.

2.3 Next, store the `count` property of the ViewModel in a local variable (`count`):

```kotlin
@Composable
fun CounterHistoryApp(
    viewModel: CounterViewModel = viewModel()
) {
    val count = viewModel.count
}
```

2.4 Now, prepare a `Surface` as the main UI container:

```kotlin
@Composable
fun CounterHistoryApp(
    viewModel: CounterViewModel = viewModel()
) {
    val count = viewModel.count

    Surface(
        modifier = Modifier.fillMaxSize(),
        color = MaterialTheme.colorScheme.background
    ) {

    }
}
```

2.5 Include a  `Column` inside the `Surface`:

```kotlin
@Composable
fun CounterHistoryApp(
    viewModel: CounterViewModel = viewModel()
) {
    val count = viewModel.count

    Surface(
        ...
    ) {

        Column(
            modifier = Modifier.fillMaxSize(),
            verticalArrangement = Arrangement.Center,
            horizontalAlignment = Alignment.CenterHorizontally
        ) {

        }
    }
}
```

2.6 The `Column` will stack two elements. The first one is a `Text` that displays the counter:

```kotlin
@Composable
fun CounterHistoryApp(
    viewModel: CounterViewModel = viewModel()
) {
    val count = viewModel.count

    Surface(
        ...
    ) {

        Column(
            ...
        ) {

            Text(
                text = count.toString(),
                style = MaterialTheme.typography.displayLarge
            )



        }
    }
}
```

2.7 The second element in the  `Column` is a `Row` element that displays buttons for each method in the ViewModel (increment, decrement, and reset):

```kotlin
@Composable
fun CounterHistoryApp(
    viewModel: CounterViewModel = viewModel()
) {
    val count = viewModel.count

    Surface(
        ...
    ) {

        Column(
            ...
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

        }
    }
}
```

2.8 Finally, use the screen in the `onCreate` method:

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            CounterHistoryApp()
        }
    }
}
```

This code displays the current state and lets the user interact with it.

### Build and Run the app

See the app working

### Summary

- `CounterHistoryApp()` is a composable screen.
- `viewModel()` creates or retrieves the same `CounterViewModel` instance for this screen.
- `val count = viewModel.count` reads the current counter value.
- `Text(text = count.toString())` shows the current value.
- `Button(onClick = { ... })` calls the ViewModel methods when the user taps a button.
- `Surface` and `MaterialTheme` keep the app styled and readable.

The UI is no longer directly calculating or storing values. It only shows state and triggers actions.

---

## Why use `viewModel()`?

`viewModel()` does two important things:

1. it creates a `CounterViewModel` when needed
2. it keeps the same instance while the screen is alive

This means the number does not disappear just because the activity is recreated due to rotation.

When the screen rotates, Android may recreate the Activity. Without a `ViewModel`, the state can disappear. `ViewModel` helps preserve the current UI state across configuration changes.

That is the main reason we are using it here.

---

## Expected behavior

Build and run the app:

- Press `+` to increase the counter
- Press `-` to decrease the counter
- Rotate the device
- Press `Reset` to set the value back to 0

The code works, but we still need one more important user-facing feature: history tracking.

## Next step

We will improve the app by showing the value history in a list, making the feature clearly visible and useful.

[Next: 04 — Add the history list](04-history-tracker.md)
