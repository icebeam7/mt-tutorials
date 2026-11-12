# 05 — Build the product list UI

In this step, we build the main screen that shows products in a vertical scrollable list, allows search, and opens the detail screen.

The list screen should not know how the database works. It only asks the ViewModel for the current list and reacts to user input. This is exactly how the app stays organized.

## 5.1 Create the list screen

Start with a `ProductListScreen` composable. 

This screen does three main things:

- shows a search field
- shows a button to create a new product
- displays all products in a vertical list

Part of the implementation includes the following:

- We will include a ViewModel instance with it.
- `collectAsStateWithLifecycle()` listens to the `StateFlow` from the ViewModel.
- A `LazyColumn` is a vertical and scrollable component to display list items.
- Each item displays the product name and price.
- When the user taps an item, we navigate to the detail screen using the product ID.
- The route `product_detail/-1` is used for creating a new product, while `product_detail/{id}` is used for an existing product.
- In the detail screen, the value of `productId` decides whether the screen behaves like a create flow or an edit/delete flow.

Here is the code:

```kotlin
@Composable
fun ProductListScreen(
    navController: NavController,
    viewModel: ProductViewModel
) {
    val products by viewModel.products.collectAsStateWithLifecycle()
    var searchText by remember { mutableStateOf("") }

    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        OutlinedTextField(
            value = searchText,
            onValueChange = {
                searchText = it
                viewModel.onSearchChanged(it)
            },
            modifier = Modifier.fillMaxWidth(),
            label = { Text("Search by name") }
        )

        Spacer(modifier = Modifier.height(12.dp))

        Button(
            onClick = {
                navController.navigate("product_detail/-1")
            },
            modifier = Modifier.fillMaxWidth()
        ) {
            Text("Add new product")
        }

        Spacer(modifier = Modifier.height(12.dp))

        LazyColumn(
            verticalArrangement = Arrangement.spacedBy(8.dp),
            modifier = Modifier.fillMaxSize()
        ) {
            items(products) { product ->
                ProductItem(
                    product = product,
                    onClick = {
                        navController.navigate("product_detail/${product.id}")
                    }
                )
            }
        }
    }
}
```

## 5.2 Create the product row item

Add a reusable row component that keeps each item in the list clean and readable.

Its implementation includes the following elements:

- `Card` gives the item a nice visual container.
- `Text(product.name)` shows the product name.
- `Text("$${product.price}")` shows the price in the list.
- `clickable { onClick() }` makes each item tappable.

```kotlin
@Composable
fun ProductItem(
    product: Product,
    onClick: () -> Unit
) {
    Card(
        modifier = Modifier
            .fillMaxWidth()
            .clickable { onClick() },
        elevation = CardDefaults.cardElevation(defaultElevation = 2.dp)
    ) {
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            horizontalArrangement = Arrangement.SpaceBetween,
            verticalAlignment = Alignment.CenterVertically
        ) {
            Text(product.name)
            Text("$${product.price}")
        }
    }
}
```

---

[Next: 06 — Add navigation and the detail screen](06-product-details-navigation.md)
