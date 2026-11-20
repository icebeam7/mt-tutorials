# Internet access with Retrofit, ViewModel, and Repository

This tutorial shows how to build an Android app that connects to a REST API and lets the user:

- fetch a list of reviews
- select one review from the list
- edit or update the selected item
- add a new review
- delete a review if the API supports it

The app will use:

- Kotlin
- Jetpack Compose
- Retrofit
- Kotlinx Serialization JSON deserialization
- ViewModel
- Repository
- Material 3 UI

## App idea

The API is:

```text
https://utbreviewsapi.azurewebsites.net/api/review
```

It returns review records like this:

```json
{
  "id": 1,
  "author": "Ana",
  "comment": "Very good service",
  "score": 5,
  "date": "2025-01-10T12:30:00"
}
```

The app will display the list of reviews, let the user choose one, and offer a form to change the data. A second form will create a new review.

## Learning goals

By the end of this tutorial, you will understand:

- how to add Retrofit and Kotlinx Serialization dependencies
- how to model JSON data with Kotlin data classes
- how to deserialize JSON from a web API
- how to define a repository for network operations
- how to use `ViewModel` to expose UI state
- how to load, select, edit, and create records in Compose
- how to organize Android app logic cleanly

## Architecture pattern

We will follow this pattern:

```text
Compose UI -> ViewModel -> Repository -> Retrofit API -> JSON response
```

This keeps the UI simple and avoids mixing network code with screen logic.

## Tutorial structure

1. [01-project-setup.md](01-project-setup.md) — configure the project and dependencies
2. [02-models-and-services.md](02-models-and-services.md) — create the Review model and Retrofit service
3. [03-repository-and-viewmodel.md](03-repository-and-viewmodel.md) — connect the API to the repository and ViewModel
4. [04-review-list-and-crud-ui.md](04-review-list-and-crud-ui.md) — build the app UI for listing, selecting, editing, and creating reviews

---

[Start the tutorial](01-project-setup.md)
