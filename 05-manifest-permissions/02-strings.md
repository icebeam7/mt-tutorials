# Part 2 — Strings and multilingual support

[Previous: What is the Android manifest?](01-manifest.md) | [Next: SMS and call permissions](03-permissions.md)

## Goal

Learn how Android stores text in resource files and how to support two languages.

---

## 1. Why use string resources?

Hardcoding text directly into code makes localization harder. Android encourages developers to keep text in resource files.

This allows the app to display the same text in different languages without changing the Kotlin code.

---

## 2. Default language strings

Find the `res/values/strings.xml` file in your project.

Replace its current content with the following information:

```xml
<resources>
    <string name="app_name">Permission Demo</string>
    <string name="title">Send SMS and Call</string>
    <string name="phone_hint">Phone number</string>
    <string name="message_hint">Message</string>
    <string name="send_sms">Send SMS</string>
    <string name="make_call">Call</string>
</resources>
```

---

## 3. Add Spanish support

Create a folder:

```text
res/values-es/
```

Then create a file `strings.xml` inside the folder:

And add the following content:

```xml
<resources>
    <string name="app_name">Demo de permisos</string>
    <string name="title">Enviar SMS y llamar</string>
    <string name="phone_hint">Número de teléfono</string>
    <string name="message_hint">Mensaje</string>
    <string name="send_sms">Enviar SMS</string>
    <string name="make_call">Llamar</string>
</resources>
```

Now Android can automatically use the correct text based on the device language.

---

## 4. Connect the manifest to strings

In the manifest, use:

```xml
android:label="@string/app_name"
```

This means the app title comes from the string resource instead of being hardcoded.

Example:

```xml
<application
    android:label="@string/app_name"
    android:icon="@mipmap/ic_launcher" />
```

This is a good practice because the same app can show English or Spanish without changing the manifest logic.

---

## 5. Importance

When an app supports more than one language:

- the UI is easier to maintain,
- translation work is simpler,
- the app is more accessible to global users,
- the code remains cleaner.

---

## Challenge

Choose any language different from English or Spanish. Create a third language folder such as `values-fr` and translate all labels into that language, for example, French.

[Check the table mentioned here for a list of codes you can use](https://developer.android.com/guide/topics/resources/providing-resources#AlternativeResources)

---

[Previous: What is the Android manifest?](01-manifest.md) | [Next: SMS and call permissions](03-permissions.md)
