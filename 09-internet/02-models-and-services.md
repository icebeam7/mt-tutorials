# 02 — Review model and Retrofit service

The app needs a Kotlin model that maps to the JSON returned by the API. Retrofit will use this class for JSON deserialization.

## 2.1 Define the Review data class

Create a file named `Review.kt` inside the package `com.example.reviewsapp.data.model`:

```kotlin
package com.example.reviewsapp.data.model

import kotlinx.serialization.Serializable

@Serializable
data class Review(
    val id: Int,
    val author: String,
    val comment: String,
    val score: Int,
    val date: String
)
```

This matches the API fields:

- `id`
- `author`
- `comment`
- `score`
- `date`

The API date value is in ISO 8601 format without timezone, for example:

```text
2024-01-17T13:41:00
```

## 2.2 Why deserialization matters

When Retrofit receives JSON from the API, it converts the response into Kotlin objects. This is called deserialization.

Kotlinx Serialization is the library that performs this conversion. In practice, the response:

```json
{
  "id": 1,
  "author": "Ana",
  "comment": "Very good service",
  "score": 5,
  "date": "2024-01-17T13:41:00"
}
```

becomes a Kotlin instance of `Review` automatically.

The `@Serializable` annotation is what allows the JSON library to generate the serializer for the model.

## 2.3 Define the Retrofit API interface

Create a file named `ReviewApi.kt` inside the package `com.example.reviewsapp.data.remote`:

```kotlin
package com.example.reviewsapp.data.remote

import com.example.reviewsapp.data.model.Review
import retrofit2.Response
import retrofit2.http.Body
import retrofit2.http.DELETE
import retrofit2.http.GET
import retrofit2.http.POST
import retrofit2.http.PUT
import retrofit2.http.Path

interface ReviewApi {

    @GET("api/review")
    suspend fun getReviews(): Response<List<Review>>

    @GET("api/review/{id}")
    suspend fun getReviewById(@Path("id") id: Int): Response<Review>

    @POST("api/review")
    suspend fun addReview(@Body review: Review): Response<Review>

    @PUT("api/review/{id}")
    suspend fun updateReview(@Path("id") id: Int, @Body review: Review): Response<Review>

    @DELETE("api/review/{id}")
    suspend fun deleteReview(@Path("id") id: Int): Response<Unit>
}
```

This interface defines the CRUD operations available from the server:

- `GET`: read all reviews
- `GET by id`: read a single review
- `POST`: create a new review
- `PUT`: update an existing review
- `DELETE`: delete a review

## 2.4 Create the Retrofit instance

Create a file called `RetrofitInstance.kt` inside the package `com.example.reviewsapp.data.remote`:

```kotlin
package com.example.reviewsapp.data.remote

import kotlinx.serialization.json.Json
import okhttp3.MediaType.Companion.toMediaType
import retrofit2.Retrofit
import retrofit2.converter.kotlinx.serialization.asConverterFactory

object RetrofitInstance {
    private const val BASE_URL = "https://utbreviewsapi.azurewebsites.net/"

    private val json = Json {
        ignoreUnknownKeys = true
        prettyPrint = false
        isLenient = true
    }

    val api: ReviewApi by lazy {
        Retrofit.Builder()
            .baseUrl(BASE_URL)
            .addConverterFactory(json.asConverterFactory("application/json".toMediaType()))
            .build()
            .create(ReviewApi::class.java)
    }
}
```

The `BASE_URL` ends with `/`, and the endpoint path is relative to it. That is why the interface methods use `api/review`.

This converter tells Retrofit to use Kotlinx Serialization when reading and writing JSON bodies.

---

[Next: 03 — Repository and ViewModel](03-repository-and-viewmodel.md)
