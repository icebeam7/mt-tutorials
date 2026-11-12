# 07 — Use the Database Inspector

The app is functional, but we also want to verify that data is really being stored in SQLite.

## 7.1 Open the Database Inspector

With the app running on an emulator or a connected device:

1. In Android Studio, open the `App Inspection` panel.
2. Select the running app.
3. Open `Database Inspector`.
4. Choose the app database named `products_database`.

You should see a table named `products`. Inside it, you will find rows with the columns `id`, `name`, `price`, `description`, `stock`, `category`.

**Database Inspector** is a tool helps you validate that:

- new data is inserted
- edited elements are updated
- deleted information is removed
- the database matches what the UI displays

Database Inspector is a debugging tool, but it is also a learning tool. It helps you connect the code you wrote to the real data being stored by Room.

This is a very important skill in Android development, especially when working with local persistence.

## 7.2 Test Database Inspector

Complete the following flow:

1. Add a product from the app.
2. Open Database Inspector.
3. Refresh the database.
4. Check that a new row appears in `products`.
5. Edit the product in the app.
6. Refresh the database and confirm the row changes in the database.
7. Delete the product in the app.
8. Refresh the database and verify the row disappears.

---

## Final recap

By the end of this tutorial, the app includes:

- Room database persistence
- product entity and DAO
- repository and ViewModel
- product list with search and vertical scroll
- product detail screen with conditional add/edit/delete buttons
- navigation between pages
- verification using Database Inspector

You now have a realistic pattern for building small local database apps with Jetpack Compose.
