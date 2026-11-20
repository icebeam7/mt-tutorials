# 01 — Project setup

In this step we create the Android project and add the necessary dependencies for internet access and JSON parsing.

## 1.1 Create a new project

Open Android Studio and create a new project using:

- Empty Activity
- Name: `ReviewsApp`
- Language: Kotlin
- Use Compose template

## 1.2 Add the required dependencies

Open the project-level `build.gradle.kts` file and ensure the Kotlin Android plugin and Compose settings are enabled.

Then open the module-level `build.gradle.kts` file and add these dependencies:

```kotlin
dependencies {
    implementation(platform("androidx.compose:compose-bom:2025.01.00"))
    implementation("androidx.activity:activity-compose:1.10.0")
    implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.8.7")
    implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.8.7")

    implementation("androidx.compose.ui:ui")
    implementation("androidx.compose.ui:ui-graphics")
    implementation("androidx.compose.ui:ui-tooling-preview")
    implementation("androidx.compose.material3:material3")

    implementation("com.squareup.retrofit2:retrofit:2.11.0")
    implementation("com.squareup.retrofit2:converter-kotlinx-serialization:2.11.0")
    implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:1.7.3")

    testImplementation("junit:junit:4.13.2")
    androidTestImplementation(platform("androidx.compose:compose-bom:2025.01.00"))
    androidTestImplementation("androidx.test.ext:junit:1.1.5")
    androidTestImplementation("androidx.test.espresso:espresso-core:3.5.1")
}
```

## 1.3 Add internet permission

Open the `AndroidManifest.xml` file and add:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

This is required so the app can reach the API.

## 1.4 Create the app entry point

We will prepare the main app structure so that the next sections can add the network layer and the review screen.

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

```kotlin
@Composable
fun ReviewApp() {
    Surface(color = MaterialTheme.colorScheme.background) {
        Text("Reviews app")
    }
}
```

## 1.5 Build and run

Run the app in the emulator to confirm the project compiles before we add the networking code.

At this stage the app is only a shell. The next step adds the data model and Retrofit service.

---

[Next: 02 — Review model and Retrofit service](02-models-and-services.md)
