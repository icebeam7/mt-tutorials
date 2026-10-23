# Part 3 — Location permission in the manifest and runtime request

To access the user's location, the app must declare the permission in the manifest. Moreover, the application needs to request access at runtime, so the user must accept or deny them.

## 1. Add the permission to the manifest

Open the Android Manifest file and add 2 permissions:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />

    <application ...>
    ...
```

Some information about these permissions:

### `ACCESS_COARSE_LOCATION`:

* **Precision**: Approximate location (usually within 100 meters).
* **Data Sources**: Uses only Wi-Fi and cell tower ID data (no GPS).
* **Battery Consumption**: Low battery impact.
* **Best Used For**: Features where exact positioning isn't needed, like showing local weather, regional news, or setting a general time zone.

### `ACCESS_FINE_LOCATION`

* **Precision**: Highly accurate location (typically within 5 to 50 meters, sometimes down to centimeters).
* **Data Sources**: Uses GPS (Global Positioning System) alongside Wi-Fi and cell data.
* **Battery Consumption**: Higher power consumption.
* **Best Used For**: Apps that require exact real-time tracking, such as turn-by-turn navigation, ride-sharing, fitness tracking, or geofencing.

### Critical update for Android 12 (API 31) and above

* **User Control**: The system now gives users a choice. Even if your app requests precise data (`ACCESS_FINE_LOCATION`), the user can choose to grant only approximate access.

* **Best Practice**: Google requires you to request both permissions together in your code. If you only request `ACCESS_FINE_LOCATION`, the system will automatically add `ACCESS_COARSE_LOCATION` to the prompt, but asking for both explicitly ensures your app handles the user's choices smoothly without crashing.

## 2. Request permission in code

In Kotlin, we build the example step by step. The goal is to make the flow clear and easier to understand.

### Step 2.1: Add the necessary imports

First, include the new imports you need.

```kotlin
import android.Manifest
import android.content.pm.PackageManager
import android.widget.Toast
import androidx.activity.compose.rememberLauncherForActivityResult
import androidx.activity.result.contract.ActivityResultContracts
import androidx.compose.material3.Button
import androidx.compose.material3.Text
import androidx.compose.runtime.*
import androidx.compose.ui.platform.LocalContext
import androidx.core.content.ContextCompat
```

### Step 2.2: Create the Composable function

Now declare the function that will display the screen.

```kotlin
@Composable
fun LocationPermissionExample() {

}
```

### Step 2.3: Add the variables

Inside the composable, define the context and the permission state.

```kotlin
@Composable
fun LocationPermissionExample() {
    val context = LocalContext.current
    var hasPermission by remember { mutableStateOf(false) }

}
```

### Step 2.4: Create the permission launcher

Use `rememberLauncherForActivityResult` to handle the result after the user chooses Accept or Deny.

```kotlin
@Composable
fun LocationPermissionExample() {
    // ... 

    val permissionLauncher = rememberLauncherForActivityResult(
        contract = ActivityResultContracts.RequestPermission()
    ) { granted ->
        hasPermission = granted
        if (granted) {
            Toast.makeText(
                context,
                context.getString(R.string.location_granted),
                Toast.LENGTH_LONG).show()
        } else {
            Toast.makeText(context,
                context.getString(R.string.location_denied),
                Toast.LENGTH_LONG).show()
        }
    }
}
```

### Step 2.5: Add the UI layout

Now create the visual structure: a text indicator and a button stacked vertically.

```kotlin
@Composable
fun LocationPermissionExample() {
    // ... 

    Column(
        modifier = Modifier.fillMaxSize().padding(24.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        Text(text = if (hasPermission) stringResource(R.string.location_granted) else stringResource(R.string.location_denied))

        Spacer(modifier = Modifier.height(16.dp))

        Button(
            onClick = {
                // request permission here
            }
        ) {
            Text(stringResource(R.string.permission_button))
        }
    }
}
```

### Step 2.6: Check permission and request it

Finally, add the logic that checks whether the permission is already granted or needs to be requested.

Replace the `Button` element added at the end of the previous step with the following implementation:

```kotlin
        Button(
            onClick = {
                val isGranted = ContextCompat.checkSelfPermission(
                    context,
                    Manifest.permission.ACCESS_FINE_LOCATION
                ) == PackageManager.PERMISSION_GRANTED

                if (isGranted) {
                    hasPermission = true
                } else {
                    permissionLauncher.launch(Manifest.permission.ACCESS_FINE_LOCATION)
                }
            }
        ) {
            Text("Enable location")
        }
```

### Step 2.7: Update the UI

Go to your `ScreenWithCard` composable and replace the `Button` at the end of the function with `LocationPermissionExample`, just below the `PermissionCard()` composable:

```kotlin
@Composable
fun ScreenWithCard() {
    Column(
        //...
    ) {
        //...
        PermissionCard()

        LocationPermissionExample()
    }
}
```

### Step 2.8: Build and run your app

Build and run your app. This is the initial UI:

<img width="548" height="988" alt="image" src="https://github.com/user-attachments/assets/94a9394b-2b6b-4a9d-93e5-3dc29ad9d860" />

Click the button:

<img width="458" height="1018" alt="image" src="https://github.com/user-attachments/assets/3ad89b72-ee33-47a7-b874-47b723d036a4" />

Select **While using the app**:

<img width="458" height="1018" alt="image" src="https://github.com/user-attachments/assets/35f7cb63-ce6d-40e1-bd66-082a3a03679f" />


## 3. Behavior when the user accepts or denies

When the user taps the button:

- If they grant permission, the app can continue with geographic features.
- If they deny permission, the app should show a friendly message and avoid trying to access the location.

Typical example:

```kotlin
if (granted) {
    // Continue with location logic
} else {
    // Show a message or keep the feature disabled
}
```

## 4. Why this is important

Runtime permission requests protect the user's privacy. The app must request access only when needed, explain why it needs the permission, and handle both outcomes gracefully.

## Summary

The flow is:

1. declare the permission in `AndroidManifest.xml`,
2. request it from the user in Kotlin,
3. check whether the result is granted or denied,
4. respond accordingly in the UI,
5. use the granted location to center the map on the user's position.

This next step is the map view: once we have permission, we can show the user's real location on a map instead of only displaying a boolean value.

[Next: Show the user's location on a map](04-show-user-location-on-map.md)
