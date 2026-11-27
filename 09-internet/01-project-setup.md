# 01 — Project setup

In this step we create the Android project and add the necessary dependencies for internet access and JSON parsing.

## 1.1 Create a new project

Open Android Studio and create a new project using:

- Empty Activity
- Name: `ReviewsApp`
- Language: Kotlin
- Use Compose template
- **Minimum SDK version: 26** (Important: Set the api version to 26 instead of the default version 24)

## 1.2 Add the required dependencies

Open the project-level `build.gradle.kts` file and ensure the Kotlin Android plugin and Compose settings are enabled.

Then open the module-level `build.gradle.kts` file and add these dependencies:

```kotlin
dependencies {
    implementation("androidx.lifecycle:lifecycle-viewmodel-compose") // if you get an error, try version 2.10.0
    implementation("androidx.navigation:navigation-compose:2.9.6")

    implementation("com.squareup.retrofit2:retrofit:3.0.0")
    implementation("com.squareup.retrofit2:converter-kotlinx-serialization:3.0.0")
    implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:1.8.0")
}
```

Also, in the same file, add the following element in `plugins` section:

```kotlin
plugins {
    kotlin("plugin.serialization") version "2.x.x" // use your Kotlin version, see libs.versions.toml
}
```

Sync the project.

## 1.3 Add internet permission

Open the `AndroidManifest.xml` file and add:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

This is required so the app can reach the API.

## 1.4 Create the app entry point

We will prepare the main app structure so that the next sections can add the network layer and the review screen.

```kotlin
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Surface

// ...

@Composable
fun ReviewApp() {
    Surface(color = MaterialTheme.colorScheme.background) {
        Text("Reviews app")
    }
}
```

Use the Composable in `onCreate`:

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            ReviewsAppTheme {
                ReviewApp()
            }
        }
    }
}

```

## 1.5 Build and run

Run the app in the emulator to confirm the project compiles before we add the networking code.

At this stage the app is only a shell. The next step adds the data model and Retrofit service.

---

[Next: 02 — Review model and Retrofit service](02-models-and-services.md)
