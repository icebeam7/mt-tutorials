# Step 1: Build the app

## 1. App idea

A hydration tracker typically has:
- a goal, for example 8 glasses,
- a counter for current glasses consumed,
- a button to add one glass,
- a button to reset the day,
- a message showing progress.

The UI is simple, but the user can track progress through the day

---

## 2. Create the project

In Android Studio create a new project (Empty Activity):

1. Name: `WaterTracker`
1. Package: `com.example.watertracker`
1. Language: Kotlin
1. Minimum SDK: API 24+

---

## 3. Add the main UI

Initial code for `MainActivity.kt`:

```kotlin
package com.example.watertracker

package com.example.watertracker

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.Button
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Surface
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
import androidx.compose.runtime.setValue
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

import androidx.activity.enableEdgeToEdge
import androidx.compose.material3.Scaffold
import com.example.watertracker.ui.theme.WaterTrackerTheme

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            WaterTrackerTheme {
                Scaffold(modifier = Modifier.fillMaxSize()) { innerPadding ->
                    WaterTrackerScreen()
                }
            }
        }
    }
}

@Composable
fun WaterTrackerScreen() {
    var glasses by remember { mutableStateOf(0) }
    val goal = 8

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
                text = "Daily Water Tracker",
                style = MaterialTheme.typography.headlineMedium
            )

            Text(
                text = "$glasses / $goal glasses",
                modifier = Modifier.padding(top = 16.dp),
                style = MaterialTheme.typography.headlineSmall
            )

            Button(
                onClick = { glasses++ },
                modifier = Modifier.padding(top = 24.dp)
            ) {
                Text("Add one glass")
            }

            Button(
                onClick = { glasses = 0 },
                modifier = Modifier.padding(top = 12.dp)
            ) {
                Text("Reset")
            }
        }
    }
}
```

Build, run the app on Emulator / Real Device, and test it. It should work properly.

---

[Next: lifecycle events](02-lifecycle-events.md)
