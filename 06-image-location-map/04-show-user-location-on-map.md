# Part 4 — Show the user's location on a map

Once the app receives the user's permission, we can do more than show a message. We can center the map on the user's current location and place a marker on that position.

This section uses Google Maps for Compose.

## 1. Add the required dependencies and set up an API Key

In your app-level `build.gradle.kts` file, add:

```kotlin
dependencies {
    implementation("com.google.android.gms:play-services-location:21.3.0")
    implementation("com.google.maps.android:maps-compose:6.4.1")
    implementation("com.google.android.gms:play-services-maps:18.2.0")
}
```

Also, make sure you add your Google Maps API key in `AndroidManifest.xml` inside the `<application>` element:

```xml
<meta-data
    android:name="com.google.android.geo.API_KEY"
    android:value="YOUR_API_KEY" />
```

See below links for obtaining an API KEY:

* [Get a Maps Demo Key](https://developers.google.com/maps/demo-key)
* [Set up Maps SDK for Android](https://developers.google.com/maps/documentation/android-sdk/get-api-key)

- The Maps Demo Key is a no-cost feature that lets you start prototyping with Google Maps Platform without entering billing information. However, it has limits (for example, usage may be restricted to a limited number of calls per day). It is not intended for production apps.

- A Standard Key requires full billing setup (Google Cloud Billing and entering a valid credit card), but is necessary for production applications. It has a Free tier, so if usage is low, you will not get charged.

### IMPORTANT: 

DO NOT COMMIT YOUR API KEY!!!

## 2. Use the user's location

To get the device coordinates, we can use `FusedLocationProviderClient`. We will build it progressively so it is easier to understand.

### Step 1: Start with the imports

```kotlin
import android.Manifest
import android.annotation.SuppressLint
import android.location.Location
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.unit.dp
import com.google.android.gms.location.LocationServices
import com.google.android.gms.maps.model.CameraPosition
import com.google.android.gms.maps.model.LatLng
import com.google.maps.android.compose.*
```

### Step 2: Create the Composable function

```kotlin
@SuppressLint("MissingPermission")
@Composable
fun UserLocationMapScreen(
    permissionGranted: Boolean
) {

}
```

### Step 3: Add the variables for context and location state

```kotlin
@SuppressLint("MissingPermission")
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

### Step 4: Request the last known location when permission is granted

```kotlin
@SuppressLint("MissingPermission")
@Composable
fun UserLocationMapScreen(
    permissionGranted: Boolean
) {
    // ...

    LaunchedEffect(permissionGranted) {
        if (permissionGranted) {
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

### Step 5: Display the map and the marker

```kotlin
@SuppressLint("MissingPermission")
@Composable
fun UserLocationMapScreen(
    permissionGranted: Boolean
) {
    // ...

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
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
