# Part 2 — Compose UI elements

Jetpack Compose allows us to build the screen using composable functions. We will use several basic components: `Column`, `Text`, `Button`, `Card`, and `Image`.

## 1. Basic screen structure

```kotlin
import androidx.compose.foundation.Image
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.res.painterResource
import androidx.compose.ui.unit.dp
import com.example.myapplication.R

@Composable
fun LocationScreen() {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(24.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        Image(
            painter = painterResource(id = R.drawable.map_preview),
            contentDescription = "Map preview",
            modifier = Modifier
                .fillMaxWidth()
                .height(180.dp)
        )

        Text(
            text = "Find my location",
            style = MaterialTheme.typography.headlineMedium
        )

        Text(
            text = "Use your location to explore nearby places.",
            style = MaterialTheme.typography.bodyLarge
        )

        Button(onClick = { /* request location */ }) {
            Text(text = "Enable location")
        }
    }
}
```

## 2. Add a card to organize content

A `Card` helps structure information neatly.

```kotlin
@Composable
fun PermissionCard() {
    Card(
        modifier = Modifier.fillMaxWidth()
    ) {
        Column(
            modifier = Modifier.padding(16.dp),
            verticalArrangement = Arrangement.spacedBy(8.dp)
        ) {
            Text(text = "Location access")
            Text(text = "This app needs access to your location to show nearby information.")
        }
    }
}
```

## 3. Combine the components

```kotlin
@Composable
fun ScreenWithCard() {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(24.dp),
        verticalArrangement = Arrangement.spacedBy(16.dp)
    ) {
        Image(
            painter = painterResource(id = R.drawable.map_preview),
            contentDescription = "Map preview"
        )

        Text(text = "Welcome")
        PermissionCard()

        Button(onClick = { /* action */ }) {
            Text("Continue")
        }
    }
}
```

[Next: Geolocation permissions and runtime request](03-location-permissions.md)
