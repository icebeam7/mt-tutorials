# Part 1 — Images and drawable resources

When building an Android app, images are usually stored in the `res` folder. The most common location is:

```text
app/src/main/res/drawable/
```

You can place files such as `.png`, `.jpg`, `.webp`, or `.xml` vector drawables there.

## 1. Add an image to the project

Add the `geolocation.png` image in the `drawable` folder:

- Go to the top menu and select **View** > **Tool Windows** > **Resource Manager**.

- Click the + (Add resources to the module) icon at the top left of the Resource Manager panel.

- Select **Import Drawables**.

- Browse your computer, select the `geolocation.png` file, and click **Open**.

## 2. Add string resources

The strings are already covered in the previous topic, so we only need to add the values to `strings.xml`.

Open:

```text
app/src/main/res/values/strings.xml
```

And add the following entries:

```xml
<resources>
    <string name="app_name">Image Location Demo</string>
    <string name="welcome_title">Find my location</string>
    <string name="welcome_description">Use your location to explore nearby places.</string>
    <string name="permission_button">Enable location</string>
    <string name="permission_rationale">This app needs access to your location to show nearby information.</string>
    <string name="location_denied">Location access was denied.</string>
    <string name="location_granted">Location access granted.</string>
</resources>
```

## 3. Use the resource in Compose

To display an image from the drawable folder, use `painterResource` and `Image` composable (shows a graphics resource):

```kotlin
import androidx.compose.foundation.Image
import androidx.compose.ui.res.painterResource
import com.example.imagelocationdemo.R

@Composable
fun ResourceExample() {
    Image(
        painter = painterResource(id = R.drawable.geolocation),
        contentDescription = "Geolocation map",
    )
}
```

Don't forget to use `ResourceExample` in `onCreate`.

## 4. Best practices

- Keep file names in lowercase and use underscores: `geolocation.png`.
- Prefer descriptive names: `ic_location`, `city_banner`, `profile_avatar`.
- Use `contentDescription` for accessibility.

## Summary

At this point, you know how to:

- place files in the `res/drawable` folder,
- add text resources in `strings.xml`,
- display them with Compose using `painterResource`.

[Next: Compose UI elements](02-compose-ui.md)
