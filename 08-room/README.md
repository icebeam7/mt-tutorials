# Room database with ViewModel and Compose

This tutorial introduces a practical app pattern used in real Android projects:

- a Room database stores the data
- a repository acts as the access layer
- a `ViewModel` exposes state and actions
- Compose UI renders the information
- Navigation connects a list screen with a detail screen

## App idea

The user will be able to:

- create a new product
- show all products in a scrollable list
- search products by name
- tap a product to open its detail screen
- edit product data
- delete a product

## Learning goals

By the end of this tutorial, you will understand:

- how to add Room and KSP to a project
- how to define a Room `@Entity`
- how to create a `@Dao` with queries and actions
- how to build a singleton database
- how to define a repository and use a `ViewModel`
- how to display data in a vertical `LazyColumn`
- how to use `NavHost` and screen navigation
- how to inspect the database in Android Studio

## Tutorial structure

1. [01-project-setup.md](01-project-setup.md) — create the app and the initial screen structure
2. [02-room-dependencies.md](02-room-dependencies.md) — add Room, KSP, and navigation dependencies
3. [03-entity-dao-database.md](03-entity-dao-database.md) — create the product entity, DAO, and database singleton
4. [04-repository-and-viewmodel.md](04-repository-and-viewmodel.md) — connect the database to a repository and ViewModel
5. [05-product-list-ui.md](05-product-list-ui.md) — show products in a scrollable list and search by name
6. [06-product-details-navigation.md](06-product-details-navigation.md) — add detail screen, edit/delete logic, and route-based navigation
7. [07-database-inspector.md](07-database-inspector.md) — verify the data in Android Studio’s Database Inspector

## Architecture overview

This is the pattern we will follow:

```text
Compose UI -> ViewModel -> Repository -> Room DAO -> SQLite database
```

This separation makes the app cleaner and easier to maintain.

---

[Start the tutorial](01-project-setup.md)
