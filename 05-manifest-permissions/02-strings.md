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

Create an Android Resource Directory named `values-es` under `res`.

Then create a file `strings.xml` inside the folder.

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

Open `AndroidManifest.xml`. Make sure `android:label` is set to `@string/app_name`. This means the app title comes from the string resource instead of being hardcoded.

Example:

```xml
<application
    android:label="@string/app_name"
    android:icon="@mipmap/ic_launcher" />
```

This is a good practice because the same app can show English or Spanish without changing the manifest logic.

---

## 5 Change Language Settings

Launch the emulator or use your device.

Open **Settings** app

<img width="554" height="1196" alt="image" src="https://github.com/user-attachments/assets/309818da-3152-44c2-a24e-baabfff3fcce" />

Tap on **System**

<img width="554" height="1196" alt="image" src="https://github.com/user-attachments/assets/8dc3edcb-2f95-424f-8ae5-6b1e1eb8e33a" />

Click on **Language & region**

<img width="554" height="1196" alt="image" src="https://github.com/user-attachments/assets/4a61f084-54e8-4180-a70f-bc10af02d3b0" />

Select **Add a language** and find **Español**

<img width="554" height="1196" alt="image" src="https://github.com/user-attachments/assets/d8b9e672-ae53-4720-84fa-57d3b9710d0f" />

Choose any **region** (for example, Mexico)

<img width="554" height="1196" alt="image" src="https://github.com/user-attachments/assets/873e707a-8af4-4435-92d3-5e6cb4b58f8e" />

Tap on the three dots of the newly added language and select **Move to top**

<img width="554" height="1196" alt="image" src="https://github.com/user-attachments/assets/d4bb670f-5762-4dbf-9364-35ba67e059e8" />

Select **Change**:

<img width="554" height="1196" alt="image" src="https://github.com/user-attachments/assets/75227b18-8ae6-4444-b8f6-478d216d955f" />

The device is now in Spanish:

<img width="554" height="1196" alt="image" src="https://github.com/user-attachments/assets/d1bc1845-d3b7-4269-823d-c9edac68d86e" />

---

## 6. Launch the app

Launch the app.

Press the Home button.

You will see the app name is "Demo de Permisos" (title in Spanish language)

<img width="554" height="1106" alt="image" src="https://github.com/user-attachments/assets/7b10182b-e84e-4fee-92b0-4848acb66ffd" />

Stop the app.

---

## 7. Importance

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
