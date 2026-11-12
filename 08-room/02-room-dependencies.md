# 02 — Add Room and KSP dependencies

In this step, we add the libraries the app needs for Room, KSP, and navigation.

- `androidx.room:room-runtime` for the database runtime
- `androidx.room:room-ktx` for coroutine support and Flow with Room.
- `androidx.room:room-compiler` for database code generation using KSP
- `com.google.devtools.ksp` for annotation processing
- `androidx.navigation:navigation-compose` for screen navigation
- `androidx.lifecycle:lifecycle-viewmodel-compose` for working with `ViewModel` in Compose

## 2.1 Add the KSP plugin

- `ksp` is the annotation processor that Room uses.
- `ksp` generates the code required by Room so we do not have to write all the boilerplate by hand.
- Make sure the KSP version matches your Kotlin version.

In the project-level `build.gradle.kts`, add the KSP plugin:

```kotlin
plugins {
    id("com.android.application") version "8.7.2" apply false
    id("org.jetbrains.kotlin.android") version "2.0.21" apply false
    id("com.google.devtools.ksp") version "2.0.21-1.0.27" apply false
}
```

## 2.2 Add dependencies in the app module

Below you will find the minimum setup needed for Room + Compose + navigation.

In your app-level `build.gradle.kts`, add the following dependencies (avoid duplicating existing dependencies):

```kotlin
dependencies {
    implementation("androidx.core:core-ktx:1.15.0")
    implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.8.7")
    implementation("androidx.activity:activity-compose:1.10.0")

    implementation(platform("androidx.compose:compose-bom:2025.01.00"))
    implementation("androidx.compose.ui:ui")
    implementation("androidx.compose.ui:ui-graphics")
    implementation("androidx.compose.ui:ui-tooling-preview")
    implementation("androidx.compose.material3:material3")

    implementation("androidx.navigation:navigation-compose:2.8.5")
    implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.8.7")

    implementation("androidx.room:room-runtime:2.6.1")
    implementation("androidx.room:room-ktx:2.6.1")
    ksp("androidx.room:room-compiler:2.6.1")
}
```

## 2.3 Add the app plugin section

At the top of the module file, include the following plugins to tell Gradle to enable KSP for the module:

```kotlin
plugins {
    id("com.android.application")
    id("org.jetbrains.kotlin.android")
    id("com.google.devtools.ksp")
}
```

## 2.4 Sync the project

Click on the **Sync** hyperlink to download the plugins and dependencies so we can use them in our code.

Now the project is ready to define the Room data layer.

---

[Next: 03 — Create the entity, DAO, and database](03-entity-dao-database.md)
