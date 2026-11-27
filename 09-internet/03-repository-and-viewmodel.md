# 03 — Repository and ViewModel

The repository is the layer that hides the network implementation. The UI should not call Retrofit directly. Instead, the `ViewModel` asks the repository for data, and the repository calls the API.

## 3.1 Create the repository

This repository is responsible for:

- making network calls
- handling the Retrofit response
- returning values to the `ViewModel`

Create a file named `ReviewRepository.kt` in the package `com.example.reviewsapp.data.repository`:

```kotlin
package com.example.reviewsapp.data.repository

import com.example.reviewsapp.data.model.Review
import com.example.reviewsapp.data.remote.RetrofitInstance
import retrofit2.HttpException
import java.io.IOException

class ReviewRepository {
    private val api = RetrofitInstance.api

    suspend fun getReviews(): List<Review> {
        try {
            val response = api.getReviews()
            return response
        } catch(e: HttpException) {
            return emptyList()
        } catch (e: IOException) {
            // Network errors: no Internet, timeout
            return emptyList()
        }
    }

    suspend fun getReviewById(id: Int): Review? {
        try {
            val response = api.getReviewById(id)
            return response
        } catch(e: HttpException) {
            return null
        } catch (e: IOException) {
            return null
        }
    }

    suspend fun addReview(review: Review): Review? {
        try {
            val response = api.addReview(review)
            return response
        } catch(e: HttpException) {
            return null
        } catch (e: IOException) {
            return null
        }
    }

    suspend fun updateReview(id: Int, review: Review): Review? {
        try {
            val response = api.updateReview(id, review)
            return response
        } catch(e: HttpException) {
            return null
        } catch (e: IOException) {
            return null
        }
    }

    suspend fun deleteReview(id: Int): Boolean {
        try {
            api.deleteReview(id)
            return true
        } catch(e: HttpException) {
            return false
        } catch (e: IOException) {
            return false
        }
    }
}
```

## 3.2 Create the ViewModel

The `ViewModel` has two responsibilities:

- expose state to the UI (`reviews`, `selectedReview`, `isLoading`)
- expose actions that trigger network operations (`loadReviews`, `getReviewById`, `addReview`, `updateReview`, `deleteReview`)

This keeps the screen composable focused on rendering and user interaction instead of mixing it with networking logic.

Create a file named `ReviewViewModel.kt` in the package `com.example.reviewsapp.ui.viewmodel`:

```kotlin
package com.example.reviewsapp.ui.viewmodel

import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import com.example.reviewsapp.data.model.Review
import com.example.reviewsapp.data.repository.ReviewRepository
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.flow.asStateFlow
import kotlinx.coroutines.launch

class ReviewViewModel(
    private val repository: ReviewRepository
) : ViewModel() {

    private val _reviews = MutableStateFlow<List<Review>>(emptyList())
    val reviews: StateFlow<List<Review>> = _reviews.asStateFlow()

    private val _selectedReview = MutableStateFlow<Review?>(null)
    val selectedReview: StateFlow<Review?> = _selectedReview.asStateFlow()

    private val _isLoading = MutableStateFlow(false)
    val isLoading: StateFlow<Boolean> = _isLoading.asStateFlow()

    fun loadReviews() {
        viewModelScope.launch {
            _isLoading.value = true
            val result = repository.getReviews()
            _reviews.value = result
            _isLoading.value = false
        }
    }

    fun getReviewById(id: Int) {
        viewModelScope.launch {
            _selectedReview.value = repository.getReviewById(id)
        }
    }

    fun selectReview(review: Review) {
        _selectedReview.value = review
    }

    fun addReview(review: Review) {
        viewModelScope.launch {
            val created = repository.addReview(review)
            if (created != null) {
                val current = _reviews.value.toMutableList()
                current.add(0, created)
                _reviews.value = current
            }
        }
    }

    fun updateReview(review: Review) {
        viewModelScope.launch {
            val updated = repository.updateReview(review.id, review)
            if (updated != null) {
                val current = _reviews.value.toMutableList()
                val index = current.indexOfFirst { it.id == review.id }
                if (index >= 0) {
                    current[index] = updated
                    _reviews.value = current
                }
            }
        }
    }

    fun deleteReview(id: Int) {
        viewModelScope.launch {
            val deleted = repository.deleteReview(id)
            if (deleted) {
                val current = _reviews.value.filter { it.id != id }
                _reviews.value = current
                if (_selectedReview.value?.id == id) {
                    _selectedReview.value = null
                }
            }
        }
    }
}
```

## 3.3 Create the factory in a separate file

The factory is a separate class because it is responsible only for creating the `ViewModel` and injecting the repository dependency. This keeps the constructor simple and the app architecture cleaner.

Create a file named `ReviewViewModelFactory.kt` in the package `com.example.reviewsapp.ui.viewmodel`:

```kotlin
package com.example.reviewsapp.ui.viewmodel

import androidx.lifecycle.ViewModel
import androidx.lifecycle.ViewModelProvider
import com.example.reviewsapp.data.repository.ReviewRepository

class ReviewViewModelFactory(
    private val repository: ReviewRepository
) : ViewModelProvider.Factory {
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(ReviewViewModel::class.java)) {
            return ReviewViewModel(repository) as T
        }
        throw IllegalArgumentException("Unknown ViewModel class")
    }
}
```

The repository is passed into the `ViewModel` through a factory. This is the recommended pattern when the `ViewModel` depends on a repository or any other dependency.

```kotlin
val repository = ReviewRepository()
val viewModelFactory = ReviewViewModelFactory(repository)
val viewModel: ReviewViewModel = viewModel(factory = viewModelFactory)
```

- the `ViewModel` can receive dependencies from the outside
- the code is easier to test
- the repository is the only component responsible for network access

The factory is especially helpful when you add mocking or fake repositories during testing.

---

[Next: 04 — Review list and CRUD UI](04-review-list-and-crud-ui.md)
