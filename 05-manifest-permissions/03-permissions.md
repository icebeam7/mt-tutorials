# Part 3 — SMS and call permissions

[Previous: Strings and multilingual support](02-strings.md) | [Next: Create the basic UI](04-ui.md)

## Goal

Add the required permissions for sending SMS and making a phone call.

---

## 1. Add permission entries

The manifest still declares the permission the app needs. Add the feature and permission lines before the `<application>` block:

```xml
<manifest ...>

    <uses-feature android:name="android.hardware.telephony"/>

    <uses-permission android:name="android.permission.SEND_SMS" />
    <uses-permission android:name="android.permission.CALL_PHONE" />

    <application android:label="@string/app_name"
        ...>
```

This tells Android that the app may need permission to send SMS and place a call.

**NOTE:** For both `SEND_SMS` and `CALL_PHONE` permissions, you need to include the `android.hardware.telephony` feature.

---

## 2. What do these permissions do?

### `SEND_SMS`

Used when an app wants to send an SMS text message.

### `CALL_PHONE`

Used when an app wants to start a phone call using the device dialer or calling app.

Both permissions are considered dangerous permissions on modern Android, so they must be requested at runtime.

---

## 3. Why runtime permission is required

The manifest declares what the app wants, but Android does not automatically allow access.

A runtime request is the user-facing step that asks for permission before the app performs the action.

The normal flow is:

* First, the manifest declares permission
* Then, the app checks if permission is already granted
* If not granted, app requests permission
* The user approves or denies it
* Finally, the app continues only if granted

This is important because Android protects user privacy and security.

---

## 4. Runtime request example

Use `ActivityResultContracts.RequestPermission` to ask the user at runtime.

Add the following method INSIDE `MainActivity.kt` class (below `onCreate` for example) to register a permission request for SMS:

```kotlin
private val requestSmsPermissionLauncher = registerForActivityResult(
    ActivityResultContracts.RequestPermission()
) { isGranted ->
    if (isGranted) {
        // send SMS
    } else {
        Toast.makeText(this, getString(R.string.sms_permission_error), Toast.LENGTH_SHORT).show()
    }
}
```

* `registerForActivityResult(...)` creates a launcher.
* `RequestPermission()` asks Android for one permission.
* `isGranted` tells us whether the user accepted.
* If accepted, the app calls `sendSms(...)`.
* If denied, it shows a `Toast` message.

Don't forget to import the packages above code needs.

Then, before sending a message, check the current state by adding the following method (also inside the class):

```kotlin
private fun requestSmsPermission(phone: String, message: String) {
    when {
        ContextCompat.checkSelfPermission(
            this,
            Manifest.permission.SEND_SMS
        ) == PackageManager.PERMISSION_GRANTED -> {
            sendSms(phone, message)
        }
        else -> {
            requestSmsPermissionLauncher.launch(Manifest.permission.SEND_SMS)
        }
    }
}
```

Don't forget to import the packages above code needs. You will get an error for `sendSms` because we have not defined the function yet.

The same idea works for making a phone call:

```kotlin
private val requestCallPermissionLauncher = registerForActivityResult(
    ActivityResultContracts.RequestPermission()
) { isGranted ->
    if (isGranted) {
        // call phone
    } else {
        Toast.makeText(this, getString(R.string.call_permission_error), Toast.LENGTH_SHORT).show()
    }
}

private fun requestCallPermission(phone: String) {
    when {
        ContextCompat.checkSelfPermission(
            this,
            Manifest.permission.CALL_PHONE
        ) == PackageManager.PERMISSION_GRANTED -> {
            callPhone(phone)
        }
        else -> {
            requestCallPermissionLauncher.launch(Manifest.permission.CALL_PHONE)
        }
    }
}
```

You will get an error for `callPhone` because the method has yet to be implemented.

---

## 5. Important note

A real Android app should not perform a dangerous action immediately without checking for permission. The manifest is necessary, but the permission request at runtime is what makes the app safe and user-aware.

---

[Previous: Strings and multilingual support](02-strings.md) | [Next: Create the basic UI](04-ui.md)
