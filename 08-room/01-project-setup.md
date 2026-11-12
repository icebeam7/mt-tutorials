# 01 — Project setup

In this step, we create the base Android project and set up the main screen skeleton.

## 1.1 Create a new project

Open Android Studio and create:

- Empty Activity
- Name: `ProductsApp`
- Language: Kotlin
- Use Compose template

## 1.2 Add the root composable

Create a placeholder composable for now:

```kotlin
@Composable
fun ProductApp() {
    Text("Product app")
}
```

## 1.3 Basic app structure

We will start by preparing the main app entry point and a screen container:

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            ProductsAppTheme {
                ProductApp()
            }
        }
    }
}
```

## 1.4 Build and Run the app

Build the app and run it in order to have the emulator/device ready.

At this stage, the project is only a shell. The next steps will add the database and the real navigation flow.

---

[Next: 02 — Add Room and KSP dependencies](02-room-dependencies.md)
