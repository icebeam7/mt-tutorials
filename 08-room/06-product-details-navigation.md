# 06 — Add navigation and the detail screen

This step creates the detail screen, adds navigation, and handles the logic for creating, editing, and deleting products.

## 6.1 Define the routes

At the app root, define the navigation graph and create the shared repository. Then pass that repository to the `ViewModel` factory.

- This sets the navigation flow between the list and the detail screen.
- `NavHost` is the main route container.
- `product_detail/{productId}` receives the selected product ID.
- The route is important because it tells the screen whether it should behave like a creation flow or an edit flow:
    - `product_detail/-1` means “new product”
    - `product_detail/{id}` means “existing product"

Here is the code:

```kotlin
@Composable
fun ProductApp() {
    val navController = rememberNavController()
    val context = LocalContext.current
    val database = ProductDatabase.getInstance(context.applicationContext)
    val repository = ProductRepository(database.productDao())

    NavHost(
        navController = navController,
        startDestination = "product_list"
    ) {
        composable("product_list") {
            val viewModel: ProductViewModel = viewModel(
                factory = ProductViewModelFactory(repository)
            )
            ProductListScreen(navController, viewModel)
        }

        composable(
            route = "product_detail/{productId}",
            arguments = listOf(
                navArgument("productId") { type = NavType.LongType }
            )
        ) { backStackEntry ->
            val productId = backStackEntry.arguments?.getLong("productId") ?: 0L
            val viewModel: ProductViewModel = viewModel(
                factory = ProductViewModelFactory(repository)
            )

            ProductDetailScreen(
                navController = navController,
                viewModel = viewModel,
                productId = productId
            )
        }
    }
}
```

## 6.2 Create the detail screen skeleton

This screen handles two cases:

- creating a new product
- editing or deleting an existing product

The key idea is this:

```kotlin
val isNewProduct = productId == -1L
```

If it is `true`, the screen is in create mode. If it is `false`, the screen is in edit mode.

Here is the initial detail screen structure:

```kotlin
@Composable
fun ProductDetailScreen(
    navController: NavController,
    viewModel: ProductViewModel,
    productId: Long
) {
    val isNewProduct = productId == -1L

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        Button(onClick = { navController.popBackStack() }) {
            Text("Back")
        }

        Spacer(modifier = Modifier.height(16.dp))

        if (isNewProduct) {
            // add mode
        } else {
            // edit mode
        }
    }
}
```

## 6.3 Add the product form state

We now define the product fields that the user will edit.

These values are stored in local Compose state and will later be converted into a Product instance

The code for the local variables goes inside the `ProductDetailScreen` function, after setting `isNewProduct` and the `Column` that contains the UI.

```kotlin
@Composable
fun ProductDetailScreen(
    // ...
) {
    val isNewProduct = // ...

    var name by remember { mutableStateOf("") }
    var price by remember { mutableStateOf("0.0") }
    var description by remember { mutableStateOf("") }
    var stock by remember { mutableStateOf("0") }
    var category by remember { mutableStateOf("") }

    Column(
        // ...
    ) {
        // ...
    }
}
```

- `name` is a string field
- `price` is stored as a string first so the user can type it naturally
- `stock` is stored as a string until we convert it to `Int`
- `category` and `description` are text fields

## 6.4 Add the UI controls

Now we add the actual controls the user will interact with on the detail screen.

Use a scrollable column so the form stays usable even with several fields.

This way, the detail screen is no longer just a placeholder. It now contains the required product fields and it is scrollable, which makes the app more usable.

The code goes below the `Spacer` element, inside the `Column` element in `ProductDetailScreen`:

```kotlin
@Composable
fun ProductDetailScreen(
    // ...
) {
    // ...

    Column(
        // ...
    ) {
        // ...
        
        Spacer(
            // ...
        )

        Text(
            text = if (isNewProduct) "New Product" else "Product Details",
            style = MaterialTheme.typography.headlineSmall
        )

        OutlinedTextField(
            value = name,
            onValueChange = { name = it },
            modifier = Modifier.fillMaxWidth(),
            label = { Text("Name") }
        )

        OutlinedTextField(
            value = price,
            onValueChange = { price = it },
            modifier = Modifier.fillMaxWidth(),
            label = { Text("Price") }
        )

        OutlinedTextField(
            value = description,
            onValueChange = { description = it },
            modifier = Modifier.fillMaxWidth(),
            label = { Text("Description") }
        )

        OutlinedTextField(
            value = stock,
            onValueChange = { stock = it },
            modifier = Modifier.fillMaxWidth(),
            label = { Text("Stock") }
        )

        OutlinedTextField(
            value = category,
            onValueChange = { category = it },
            modifier = Modifier.fillMaxWidth(),
            label = { Text("Category") }
        )
    }
}
```

## 6.5 Show the conditional buttons

Now we add the logic that decides which action buttons appear.

- if it is a new product, show an `Add` button
- if it is an existing product, show `Edit` and `Delete` buttons

The condition is based on the route value:

- `-1` = new product
- any other ID = existing product

Replace the `if (isNewProduct)` condition in the `Column` inside `ProductDetailScreen`:

```kotlin
if (isNewProduct) {
    Button(
        onClick = {
            // add to database
        },
        modifier = Modifier.fillMaxWidth()
    ) {
        Text("Add")
    }
} else {
    Row(
        modifier = Modifier.fillMaxWidth(),
        horizontalArrangement = Arrangement.spacedBy(8.dp)
    ) {
        Button(
            onClick = {
                // edit product
            },
            modifier = Modifier.weight(1f)
        ) {
            Text("Edit")
        }

        Button(
            onClick = {
                // delete product
            },
            modifier = Modifier.weight(1f),
            colors = ButtonDefaults.buttonColors(
                containerColor = MaterialTheme.colorScheme.error
            )
        ) {
            Text("Delete")
        }
    }
}
```

## 6.6 Create the product object before saving

Once the user has filled the form, we convert the current values into a `Product` object before calling the database methods.

This is the main idea. **Do not add this code**:

```kotlin
val updatedProduct = Product(
    id = if (isNewProduct) 0 else productId,
    name = name,
    price = price.toDoubleOrNull() ?: 0.0,
    description = description,
    stock = stock.toIntOrNull() ?: 0,
    category = category
)
```

This transforms the form state into a real entity to store in Room.

- `id = 0` is used for a new product because Room will generate the ID automatically.
- `id = productId` is used when editing an existing record.
- `toDoubleOrNull()` and `toIntOrNull()` keep the app safe from invalid typing.

## 6.7 Save, edit, and delete logic

Now we connect the button actions to the ViewModel and navigation. 

This is the final behavior: create, edit, or delete the product (depending on the Button that was clicked) and return to the list.

Implement the `onClick` callback of each `Button`:

- `viewModel.addProduct(updatedProduct)` stores a new row.
- `viewModel.editProduct(updatedProduct)` updates the selected row.
- `viewModel.deleteProduct(product)` removes it from the database.

```kotlin
if (isNewProduct) {
    Button(
        onClick = {
            val updatedProduct = Product(
                id = 0,
                name = name,
                price = price.toDoubleOrNull() ?: 0.0,
                description = description,
                stock = stock.toIntOrNull() ?: 0,
                category = category
            )
            viewModel.addProduct(updatedProduct)
            navController.popBackStack()
        },
        modifier = Modifier.fillMaxWidth()
    ) {
        Text("Add")
    }
} else {
    Row(
        modifier = Modifier.fillMaxWidth(),
        horizontalArrangement = Arrangement.spacedBy(8.dp)
    ) {
        Button(
            onClick = {
                val updatedProduct = Product(
                    id = productId,
                    name = name,
                    price = price.toDoubleOrNull() ?: 0.0,
                    description = description,
                    stock = stock.toIntOrNull() ?: 0,
                    category = category
                )
                viewModel.editProduct(updatedProduct)
                navController.popBackStack()
            },
            modifier = Modifier.weight(1f)
        ) {
            Text("Edit")
        }

        Button(
            onClick = {
                viewModel.deleteProduct(product)
                navController.popBackStack()
            },
            modifier = Modifier.weight(1f),
            colors = ButtonDefaults.buttonColors(
                containerColor = MaterialTheme.colorScheme.error
            )
        ) {
            Text("Delete")
        }
    }
}
```

## 6.8 Build and run the app

Build the app, check if you can:

- add three products
- see the list of products
- select a product and update its price
- select a product and delete it

---

[Next: 07 — Use the Database Inspector](07-database-inspector.md)
