# 03 — Create the entity, DAO, and database

Now we create the data layer. We will define: 

- the product table
- the database access API
- and a singleton database instance.

## 3.1 Create the product entity

A data class represents a row in the a table.

Create a new file named `Product.kt`.

We will inclde:

- `@Entity(tableName = "products")` annotation to tell Room to create a table named `products` from this class.
- `@PrimaryKey(autoGenerate = true)` annotation gives each product a unique ID, and sets that field to be the table's primary key.
- `name`, `price`, `description`, `stock`, and `category` are table columns.

Here is the code:

```kotlin
@Entity(tableName = "products")
data class Product(
    @PrimaryKey(autoGenerate = true)
    val id: Long = 0,
    val name: String,
    val price: Double,
    val description: String = "",
    val stock: Int = 0,
    val category: String = ""
)
```

## 3.2 Create the DAO

The DAO exposes the operations available to the app.

Create a file named `ProductDao.kt`.

We will include the following:

- A `getAllProducts()` method that returns all rows as a `Flow`.
- A `searchByName()` method that filters the list by product name.
- An `insert()` method that saves a new record.
- An `update()` method that edits an existing record.
- A `delete()` method that removes a record.

A `Flow` is useful because the UI can react automatically when the database changes.

Here is the code:

```kotlin
@Dao
interface ProductDao {

    @Query("SELECT * FROM products ORDER BY name ASC")
    fun getAllProducts(): Flow<List<Product>>

    @Query("SELECT * FROM products WHERE LOWER(name) LIKE '%' || LOWER(:query) || '%' ORDER BY name ASC")
    fun searchByName(query: String): Flow<List<Product>>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insert(product: Product): Long

    @Update
    suspend fun update(product: Product)

    @Delete
    suspend fun delete(product: Product)
}
```

## 3.3 Create the database singleton

By using the Singleton pattern, we will ensure there is only one database instance in the app. 

This pattern prevents opening multiple database instances and keeps the app efficient.

The following elements will be included:

- A class-level `@Database` annotation defines the database and the entities it contains.
- `Room.databaseBuilder(...)` is a method that creates the SQLite database file.
- The companion object stores a singleton using `synchronized`.

Create a file named `ProductDatabase.kt`.

```kotlin
@Database(
    entities = [Product::class],
    version = 1,
    exportSchema = false
)
abstract class ProductDatabase : RoomDatabase() {
    abstract fun productDao(): ProductDao

    companion object {
        @Volatile
        private var INSTANCE: ProductDatabase? = null

        fun getInstance(context: Context): ProductDatabase {
            return INSTANCE ?: synchronized(this) {
                val instance = Room.databaseBuilder(
                    context.applicationContext,
                    ProductDatabase::class.java,
                    "products_database"
                )
                    .fallbackToDestructiveMigration()
                    .build()
                INSTANCE = instance
                instance
            }
        }
    }
}
```

---

[Next: 04 — Add the repository and ViewModel](04-repository-and-viewmodel.md)
