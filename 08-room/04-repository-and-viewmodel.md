# 04 — Add the repository and ViewModel

Now we create the app logic layer that sits between the database and the UI.

With this layer:

- the UI only reacts to state
- the database is hidden behind a cleaner API
- the logic is easier to maintain

## 4.1 Create the repository

- The repository wraps the DAO and exposes convenient methods for the rest of the app.
- The database logic remains in the DAO.
- The repository isolates the implementation and keeps the UI simple.
- This is the standard architecture pattern for Android apps.

Create a file named `ProductRepository.kt`. Here is the code:

```kotlin
class ProductRepository(private val productDao: ProductDao) {

    val allProducts: Flow<List<Product>> = productDao.getAllProducts()

    fun searchProducts(query: String): Flow<List<Product>> {
        return productDao.searchByName(query)
    }

    suspend fun insertProduct(product: Product): Long {
        return productDao.insert(product)
    }

    suspend fun updateProduct(product: Product) {
        productDao.update(product)
    }

    suspend fun deleteProduct(product: Product) {
        productDao.delete(product)
    }
}
```

## 4.2 Create the ViewModel

- The ViewModel exposes UI state and actions for the list and detail screens.
- The ViewModel receives the repository as a dependency
- `StateFlow` is a reactive state container used by Compose.
- `flatMapLatest` reacts to changes in the search query. Returns a flow that switches to a new flow produced by transform function every time the original flow emits a value.
- `stateIn(...)` exposes the list as a stream the UI can collect.
- The `addProduct`, `editProduct`, and `deleteProduct` functions delegate to the repository.
- This keeps the screen free of database concerns.

Create a file called `ProductViewModel.kt`. Here is the code:

```kotlin
class ProductViewModel(
    private val repository: ProductRepository
) : ViewModel() {

    private val _searchQuery = MutableStateFlow("")
    val searchQuery: StateFlow<String> = _searchQuery.asStateFlow()

    val products: StateFlow<List<Product>> =
        _searchQuery
            .flatMapLatest { query ->
                if (query.isBlank()) {
                    repository.allProducts
                } else {
                    repository.searchProducts(query)
                }
            }
            .stateIn(
                scope = viewModelScope,
                started = SharingStarted.WhileSubscribed(5000),
                initialValue = emptyList()
            )

    fun onSearchChanged(value: String) {
        _searchQuery.value = value
    }

    suspend fun addProduct(product: Product) {
        repository.insertProduct(product)
    }

    suspend fun editProduct(product: Product) {
        repository.updateProduct(product)
    }

    suspend fun deleteProduct(product: Product) {
        repository.deleteProduct(product)
    }
}
```

### 4.3 Add a ViewModel factory

- Because the ViewModel constructor receives a repository, we need a factory to create it. 
- The ViewModel is not tied to the Android context
- It is easier to test
- The repository remains the single place that talks to the database
- The screen only asks the ViewModel for state and actions

Create a `ProductViewModelFactory.kt` file with the following code:

```kotlin
class ProductViewModelFactory(
    private val repository: ProductRepository
) : ViewModelProvider.Factory {

    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(ProductViewModel::class.java)) {
            @Suppress("UNCHECKED_CAST")
            return ProductViewModel(repository) as T
        }
        throw IllegalArgumentException("Unknown ViewModel class")
    }
}
```

---

[Next: 05 — Build the product list UI](05-product-list-ui.md)
