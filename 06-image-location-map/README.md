# Resources, Compose UI, and Location Permissions

This tutorial shows how to work with drawable resources, add string values, build a simple user interface with Jetpack Compose, and request the user's location permission at runtime in order to display it in a map.

## Tutorial structure

1. [Part 1 — Images and drawable resources](01-images-and-resources.md)
2. [Part 2 — Compose UI elements](02-compose-ui.md)
3. [Part 3 — Location permission in the manifest and runtime request](03-location-permissions.md)
4. [Part 4 — Show the user's location on a map](04-show-user-location-on-map.md)

## Learning goals

- Use images from the app resources folder.
- Add string values in `strings.xml`.
- Build basic UI components with Compose.
- Request location permission from the user.
- Handle accept/deny results with Kotlin.
- Display the user's location on a map after permission is granted.

## Project idea

We will create a small screen that:

- displays an image from `res/drawable`,
- shows text from `strings.xml`,
- contains simple UI elements such as buttons and cards,
- asks for the device location when the user taps a button,
- shows the user’s current location on a map after permission is approved.

## Step 0

Create a project named `ImageLocationDemo`, leave the rest of options with their default values.

[Start with Part 1](01-images-and-resources.md)
