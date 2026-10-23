# Part 2 — Compose UI elements

Jetpack Compose allows us to build the screen using composable functions. We will use several basic components: `Column`, `Text`, `Button`, `Card`, and `Image`.

## 1. Import required packages

Use the following imports:

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.unit.dp
import androidx.compose.ui.res.stringResource
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
            Text(text = stringResource(R.string.welcome_title))
            Text(text = stringResource(R.string.welcome_description))
        }
    }
}
```

## 3. Create a basic UI

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
            painter = painterResource(id = R.drawable.geolocation),
            contentDescription = "Map preview"
        )

        Text(text = stringResource(R.string.app_name))
        PermissionCard()

        Button(onClick = { /* action */ }) {
            Text(stringResource(R.string.permission_button))
        }
    }
}
```

Call this new composable in `onCreate`.

Build and run your app:

<img width="548" height="1112" alt="image" src="https://github.com/user-attachments/assets/8966354d-8dd8-4f2d-a2cc-5341e91f27a8" />


[Next: Geolocation permissions and runtime request](03-location-permissions.md)
