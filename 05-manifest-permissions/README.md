# Manifest and permissions tutorial

This tutorial introduces you to the Android manifest, localization with string resources, and the permissions needed for SMS and phone call actions.

## Tutorial structure

1. [Part 1 — What is the Android manifest?](01-manifest.md)
2. [Part 2 — Strings and multilingual support](02-strings.md)
3. [Part 3 — SMS and call permissions](03-permissions.md)
4. [Part 4 — Create the basic UI](04-ui.md)

![Manifest overview](01-manifest-overview.svg)

---

## Core idea

The Android manifest is the app's declaration file. It tells Android what the app is, which activities it contains, which permissions it needs, and how the app should launch.

Permissions are important because they protect the user and the device. For example, sending SMS or making a call are actions that require special app rights.

---

## Learning goals

- Understand the purpose of AndroidManifest.xml
- Use resources for text that can support two languages
- Add permission entries for SMS and call actions
- Build a basic layout designed for these actions
- Practice with real examples and small challenges

[Next: What is the Android manifest?](01-manifest.md)
