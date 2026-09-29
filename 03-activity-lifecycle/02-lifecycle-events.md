# Step 2: See the lifecycle callbacks in real time

## Goal

Add logs to the app so students can see exactly when each lifecycle callback runs.

We will track six events:

- `onCreate()`
- `onStart()`
- `onResume()`
- `onPause()`
- `onStop()`
- `onDestroy()`

---

## 1. Add logging and simple persistence

Update `MainActivity.kt` to log all lifecycle methods and store the water value in `SharedPreferences`:

1. Import the following packages:

```kotlin
import android.content.Context
import android.content.SharedPreferences
import android.util.Log
```

2. Add below elements before `onCreate`:

```kotlin
    private val TAG = "WaterTrackerLifecycle"
    private lateinit var prefs: SharedPreferences
    private var glasses = 0
```

3. Create new functions to handle each of the lifecycle events:

```kotlin
    override fun onStart() {
        super.onStart()
        glasses = prefs.getInt("glasses", 0)
        Log.d(TAG, "onStart() called. Reading latest value from storage: $glasses")
    }

    override fun onResume() {
        super.onResume()
        Log.d(TAG, "onResume() called. App is interactive. Current glasses: $glasses")
    }

    override fun onPause() {
        super.onPause()
        saveGlasses()
        Log.d(TAG, "onPause() called. Saved current value before leaving screen: $glasses")
    }

    override fun onStop() {
        super.onStop()
        saveGlasses()
        Log.d(TAG, "onStop() called. Final save before app becomes invisible: $glasses")
    }

    override fun onDestroy() {
        saveGlasses()
        Log.d(TAG, "onDestroy() called. Last value saved: $glasses")
        super.onDestroy()
    }

    private fun saveGlasses() {
        prefs.edit().putInt("glasses", glasses).apply()
    }
```

4. Modify `WaterTrackerScreen`:

```kotlin
fun WaterTrackerScreen(
    glasses: Int,
    onAddGlass: () -> Unit,
    onReset: () -> Unit
) { 
    // var glasses by remember { mutableStateOf(0) }
    ... 

            Button(
                onClick = onAddGlass,
                modifier = Modifier.padding(top = 24.dp)
            ) {
                Text("Add one glass")
            }

            Button(
                onClick = onReset,
                modifier = Modifier.padding(top = 12.dp)
            ) {
                Text("Reset")
            }

}
```

5. Modify `onCreate` so previous code is connected:

```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()

        prefs = getSharedPreferences("water_tracker_prefs", Context.MODE_PRIVATE)

        glasses = prefs.getInt("glasses", 0)
        Log.d(TAG, "onCreate() called. Loaded glasses from storage: $glasses")

        setContent {
            WaterTrackerTheme {
                Scaffold(modifier = Modifier.fillMaxSize()) { innerPadding ->
                    WaterTrackerScreen(
                        glasses = glasses,
                        onAddGlass = {
                            glasses++
                            saveGlasses()
                            Log.d(TAG, "User added a glass. Total now: $glasses")
                        },
                        onReset = {
                            glasses = 0
                            saveGlasses()
                            Log.d(TAG, "User reset progress to 0")
                        }
                    )
                }
            }
        }
    }
```

Build, run, and test the app.

---

## 2. Where to see the logs

In Android Studio:

- open the **Logcat** tab (it has a Cat icon),
- make sure the device is selected,
- filter by `WaterTrackerLifecycle` or by the app package name,
- watch the events as the app opens and closes.

---

## 3. Test lifecycle events

You should see something similar to this in the Logcat:

```text
D/WaterTrackerLifecycle: onCreate() called. Loaded glasses from storage: 0
D/WaterTrackerLifecycle: onStart() called. Reading latest value from storage: 0
D/WaterTrackerLifecycle: onResume() called. App is interactive. Current glasses: 0
```

Send the app to background (tap Home / Back Button or navigate to another app / close the app). You will see any of these messages:

```text
D/WaterTrackerLifecycle: onPause() called. Saved current value before leaving screen: 4
D/WaterTrackerLifecycle: onStop() called. Final save before app becomes invisible: 4
D/WaterTrackerLifecycle: onDestroy() called. Last value saved: 4
```

---

## 4. Remember the lifecycle events

- `onCreate()` is called first when the Activity is created,
- `onStart()` happens when the screen becomes visible,
- `onResume()` happens when it is interactive,
- `onPause()` happens when another screen takes focus,
- `onStop()` happens when it is no longer visible,
- `onDestroy()` happens when it is actually destroyed.

This becomes much easier to remember when you can see the logs.

Besides, we are using the events to our advantage:

- `onCreate()` reads saved data,
- `onStart()` checks if the latest value is available,
- `onPause()` and `onStop()` save the current state,
- `onDestroy()` confirms the final save,

---

[Next: rotation and state](03-rotation-and-state.md)
