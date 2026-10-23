# Part 4 — Show the user's location on a map

Once the app receives the user's permission, we can do more than show a message. We can center the map on the user's current location and place a marker on that position.

This section uses Google Maps for Compose.

**IMPORTANT**: If you are using an Android Emulaotr 

## 1. Add the required dependencies and set up an API Key

In your Module app-level `build.gradle.kts` file, add the following dependencies in the `dependencies` section:

```kotlin
    implementation("com.google.android.gms:play-services-location:21.3.0")
    implementation("com.google.maps.android:maps-compose:6.4.1")
    implementation("com.google.android.gms:play-services-maps:18.2.0")
```

Synchronize the Gradle project.

Also, make sure you set a **Google Maps API key** in the Android Manifest inside the `<application>` element:

```xml
<meta-data
    android:name="com.google.android.geo.API_KEY"
    android:value="YOUR_API_KEY" />
```

Replace the `YOUR_API_KEY` placeholder with the actual **Google Maps API Key**.

* You can find a Google Maps API Key in Moodle (**Task 06. Images, Geolocation, and Map**)

* You can also create your own Standard API Key: [Set up Maps SDK for Android](https://developers.google.com/maps/documentation/android-sdk/get-api-key)

    - A Standard Key requires full billing setup (Google Cloud Billing and entering a valid credit card), but is necessary for production applications. It has a Free tier, so if usage is low, you will not get charged.

### IMPORTANT: 

- DO NOT COMMIT THIS API KEY!!! (Remove it before submitting the assignment)

- Moreover, this is not the safest approach. In a later exercise, we will explore **secrets** to protect sensitive information.

## 2. Use the user's location

To get the device coordinates, we can use `FusedLocationProviderClient`. We will build it progressively so it is easier to understand.

### Step 2.1: Start with the imports

```kotlin
import android.annotation.SuppressLint
import android.location.Location
import androidx.compose.foundation.layout.*
import com.google.android.gms.location.LocationServices
import com.google.android.gms.maps.model.CameraPosition
import com.google.android.gms.maps.model.LatLng
import com.google.maps.android.compose.*
```

### Step 2.2: Create the Composable function

```kotlin
@Composable
fun UserLocationMapScreen(
    permissionGranted: Boolean
) {

}
```

### Step 2.3: Add the variables for context and location state

```kotlin
@Composable
fun UserLocationMapScreen(
    permissionGranted: Boolean
) {
    val context = LocalContext.current
    val fusedLocationClient = remember {
        LocationServices.getFusedLocationProviderClient(context)
    }

    var userLocation by remember { mutableStateOf<LatLng?>(null) }
    val cameraPositionState = rememberCameraPositionState {
        position = CameraPosition.fromLatLngZoom(LatLng(0.0, 0.0), 10f)
    }
}
```

### Step 2.4: Request the last known location when permission is granted

```kotlin
@Composable
fun UserLocationMapScreen(
    permissionGranted: Boolean
) {
    // ...

    LaunchedEffect(permissionGranted) {
        if (permissionGranted) {
            @SuppressLint("MissingPermission")
            fusedLocationClient.lastLocation.addOnSuccessListener { location: Location? ->
                location?.let {
                    val current = LatLng(it.latitude, it.longitude)
                    userLocation = current
                    cameraPositionState.position = CameraPosition.fromLatLngZoom(current, 15f)
                }
            }
        }
    }
}
```

### Step 2.5: Display the map and the marker

```kotlin
@Composable
fun UserLocationMapScreen(
    permissionGranted: Boolean
) {
    // ...

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(top = 12.dp)
    ) {
        Text(text = "Your current location")

        Spacer(modifier = Modifier.height(12.dp))

        GoogleMap(
            modifier = Modifier
                .fillMaxWidth()
                .height(420.dp),
            cameraPositionState = cameraPositionState
        ) {
            userLocation?.let { position ->
                Marker(
                    state = MarkerState(position = position),
                    title = "You are here"
                )
            }
        }
    }
}
```

### Step 2.6: Modify `ScreenWithCard`:

Move permission state up into `ScreenWithCard()` to conditionally show: 

- Permission UI when not granted
- `UserLocationMapScreen(permissionGranted = true)` when granted.

We will set `hasPermission` and add a validation after `PermissionCard` instead of just invoking `LocationPermissionExample`:

```kotlin
@Composable
fun ScreenWithCard() {
    var hasPermission by remember { mutableStateOf(false) }

    Column(
        // ...
    ) {
        // LocationPermissionExample()

        if (hasPermission) {
            UserLocationMapScreen(permissionGranted = true)
        } else {
            LocationPermissionExample(
                onPermissionResult = { granted -> hasPermission = granted }
            )
        }
    }
}
```

### Step 2.7: Modify `LocationPermissionExample`:

Now, update `LocationPermissionExample` to expose callback:

```kotlin
@Composable
fun LocationPermissionExample(
    onPermissionResult: (Boolean) -> Unit
) {
  // ...
}
```

Inside permission result set `hasPermission` and keep parent in sync if already granted at startup by invoking `LaunchedEffect`:

```kotlin
@Composable
fun LocationPermissionExample(
    onPermissionResult: (Boolean) -> Unit
) {
    val context = // ...

    var hasPermission by remember {
        mutableStateOf(
            ContextCompat.checkSelfPermission(
                context,
                Manifest.permission.ACCESS_FINE_LOCATION
            ) == PackageManager.PERMISSION_GRANTED
        )
    }

    LaunchedEffect(hasPermission) {
        onPermissionResult(hasPermission)
    }

    val permissionLauncher = rememberLauncherForActivityResult(
        contract = ActivityResultContracts.RequestPermission()
    ) { granted ->
        hasPermission = granted
        onPermissionResult(granted)

        // ...

        Button(
            onClick = {
                val isGranted = // ...

                if (isGranted) {
                    hasPermission = true
                    onPermissionResult(true)
                } else {
                    // ...
                }
            }
        ) {
            // ...
        }
    }
}
```

### Step 2.8: Set emulator location

If you are using the emulator, set the device location to any desired place with the **Extended controls**:

<img width="2262" height="1346" alt="image" src="https://github.com/user-attachments/assets/54beee2d-2596-41be-8d4e-9e769433cd6d" />

### Step 2.9: Build and run your app

<img width="518" height="1092" alt="image" src="https://github.com/user-attachments/assets/ac9d502e-7704-4642-82fa-2707c6330b7b" />

If the app does not load, try adding the INTERNET permission in the Android Manifest:

```xml
    <uses-permission android:name="android.permission.INTERNET" />
```

## 3. Summary

The flow is:

- request permission from the user,
- read the last known location with `FusedLocationProviderClient`,
- move the camera to the actual coordinates,
- place a marker on the map,
- show the user’s position visually.

## 4. Important note

Google Maps requires a valid API key and the necessary setup in the project. The sample above focuses on the logic and how the map is updated after permission is granted.

[Back to the tutorial index](README.md)
