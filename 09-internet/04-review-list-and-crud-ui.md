# 04 — Review list and CRUD UI with navigation

This step changes the UI from a single large screen into a small app with navigation between screens:

- a list screen to show all reviews
- a form screen to add a new review
- a form screen to edit an existing review

This makes the tutorial more realistic and easier to scale as the app grows.

## 4.1 Create the navigation host

Create a `ReviewNavHost` file inside the `ui/navigation` package.

```kotlin
package com.example.reviewsapp.ui.navigation

import androidx.compose.runtime.Composable
import androidx.navigation.NavType
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable
import androidx.navigation.compose.rememberNavController
import com.example.reviewsapp.data.repository.ReviewRepository
import com.example.reviewsapp.ui.screen.reviewform.ReviewFormScreen
import com.example.reviewsapp.ui.screen.reviewlist.ReviewListScreen
import com.example.reviewsapp.ui.viewmodel.ReviewViewModel
import com.example.reviewsapp.ui.viewmodel.ReviewViewModelFactory
import androidx.lifecycle.viewmodel.compose.viewModel
import androidx.navigation.navArgument

object ReviewDestinations {
    const val REVIEW_LIST = "review_list"
    const val REVIEW_FORM = "review_form"
    const val REVIEW_ID_ARG = "reviewId"
}

@Composable
fun ReviewNavHost() {
    val navController = rememberNavController()
    val reviewViewModel: ReviewViewModel = viewModel(
        factory = ReviewViewModelFactory(ReviewRepository())
    )

    NavHost(
        navController = navController,
        startDestination = ReviewDestinations.REVIEW_LIST
    ) {
        composable(ReviewDestinations.REVIEW_LIST) {
            ReviewListScreen(
                onAddReviewClick = {
                    navController.navigate(ReviewDestinations.REVIEW_FORM)
                },
                onEditReviewClick = { reviewId ->
                    navController.navigate("${ReviewDestinations.REVIEW_FORM}?${ReviewDestinations.REVIEW_ID_ARG}=$reviewId")
                },
                viewModel = reviewViewModel
            )
        }

        composable(
            route = "${ReviewDestinations.REVIEW_FORM}?${ReviewDestinations.REVIEW_ID_ARG}={${ReviewDestinations.REVIEW_ID_ARG}}",
            arguments = listOf(
                navArgument(ReviewDestinations.REVIEW_ID_ARG) {
                    type = NavType.IntType
                    defaultValue = -1
                }
            )
        ) { backStackEntry ->
            val reviewId = backStackEntry.arguments?.getInt(ReviewDestinations.REVIEW_ID_ARG) ?: -1

            ReviewFormScreen(
                reviewId = reviewId,
                onBackClick = { navController.popBackStack() },
                viewModel = reviewViewModel
            )
        }
    }
}
```

This setup keeps the navigation logic separate from the screens and makes the app easier to grow.

## 4.2 The list screen

Create a `ReviewListScreen` in `ui/screen/reviewlist`.

```kotlin
package com.example.reviewsapp.ui.screen.reviewlist

import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material3.Button
import androidx.compose.material3.Card
import androidx.compose.material3.CircularProgressIndicator
import androidx.compose.material3.ExperimentalMaterial3Api
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Scaffold
import androidx.compose.material3.Text
import androidx.compose.material3.TopAppBar
import androidx.compose.runtime.Composable
import androidx.compose.runtime.LaunchedEffect
import androidx.compose.runtime.collectAsState
import androidx.compose.runtime.getValue
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import com.example.reviewsapp.ui.viewmodel.ReviewViewModel

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun ReviewListScreen(
    onAddReviewClick: () -> Unit,
    onEditReviewClick: (Int) -> Unit,
    viewModel: ReviewViewModel
) {
    val reviews by viewModel.reviews.collectAsState()
    val isLoading by viewModel.isLoading.collectAsState()

    LaunchedEffect(viewModel) {
        viewModel.loadReviews()
    }

    Scaffold(
        topBar = {
            TopAppBar(title = { Text("Reviews") })
        },
        floatingActionButton = {
            Button(onClick = onAddReviewClick) {
                Text("Add review")
            }
        }
    ) { paddingValues ->
        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(paddingValues)
                .padding(16.dp),
            verticalArrangement = Arrangement.spacedBy(12.dp)
        ) {
            if (isLoading) {
                Column(
                    modifier = Modifier.fillMaxWidth(),
                    horizontalAlignment = Alignment.CenterHorizontally
                ) {
                    CircularProgressIndicator()
                    Text("Loading reviews...")
                }
                return@Scaffold
            }

            if (reviews.isEmpty()) {
                Text("No reviews available yet.")
                return@Scaffold
            }

            Button(onClick = { viewModel.loadReviews() }) {
                Text("Refresh reviews")
            }

            LazyColumn(
                verticalArrangement = Arrangement.spacedBy(8.dp)
            ) {
                items(reviews) { review ->
                    Card(
                        modifier = Modifier
                            .fillMaxWidth()
                            .clickable { onEditReviewClick(review.id) }
                    ) {
                        Column(
                            modifier = Modifier.padding(12.dp)
                        ) {
                            Text(text = review.author, style = MaterialTheme.typography.titleMedium)
                            Text(text = review.comment)
                            Text(text = "Score: ${review.score}")
                            Text(text = review.date)
                        }
                    }
                }
            }
        }
    }
}
```

## 4.3 The form screen

Create a `ReviewFormScreen` in `ui/screen/reviewform`.

```kotlin
package com.example.reviewsapp.ui.screen.reviewform

import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.Button
import androidx.compose.material3.ExperimentalMaterial3Api
import androidx.compose.material3.OutlinedTextField
import androidx.compose.material3.Scaffold
import androidx.compose.material3.Text
import androidx.compose.material3.TopAppBar
import androidx.compose.runtime.Composable
import androidx.compose.runtime.LaunchedEffect
import androidx.compose.runtime.collectAsState
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
import androidx.compose.runtime.setValue
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import com.example.reviewsapp.data.model.Review
import com.example.reviewsapp.ui.viewmodel.ReviewViewModel

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun ReviewFormScreen(
    reviewId: Int = -1,
    onBackClick: () -> Unit,
    viewModel: ReviewViewModel
) {
    val selectedReview by viewModel.selectedReview.collectAsState()

    var author by remember { mutableStateOf("") }
    var comment by remember { mutableStateOf("") }
    var score by remember { mutableStateOf("") }
    var showDeleteDialog by remember { mutableStateOf(false) }

    LaunchedEffect(reviewId) {
        if (reviewId != -1) {
            viewModel.getReviewById(reviewId)
        }
    }

    LaunchedEffect(selectedReview) {
        selectedReview?.let {
            author = it.author
            comment = it.comment
            score = it.score.toString()
        }
    }

    if (showDeleteDialog) {
        androidx.compose.material3.AlertDialog(
            onDismissRequest = { showDeleteDialog = false },
            title = { Text("Delete review?") },
            text = { Text("This action cannot be undone.") },
            confirmButton = {
                Button(
                    onClick = {
                        if (reviewId != -1) {
                            viewModel.deleteReview(reviewId)
                        }
                        showDeleteDialog = false
                        onBackClick()
                    }
                ) {
                    Text("Delete")
                }
            },
            dismissButton = {
                Button(onClick = { showDeleteDialog = false }) {
                    Text("Cancel")
                }
            }
        )
    }

    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text(if (reviewId == -1) "Add review" else "Edit review") },
                navigationIcon = {
                    Button(onClick = onBackClick) {
                        Text("Back")
                    }
                }
            )
        }
    ) { paddingValues ->
        Column(
            modifier = Modifier
                .padding(paddingValues)
                .padding(16.dp),
            verticalArrangement = Arrangement.spacedBy(12.dp)
        ) {
            OutlinedTextField(
                value = author,
                onValueChange = { author = it },
                label = { Text("Author") },
                modifier = Modifier.fillMaxWidth()
            )

            OutlinedTextField(
                value = comment,
                onValueChange = { comment = it },
                label = { Text("Comment") },
                modifier = Modifier.fillMaxWidth()
            )

            OutlinedTextField(
                value = score,
                onValueChange = { score = it },
                label = { Text("Score") },
                modifier = Modifier.fillMaxWidth()
            )

            Row(
                horizontalArrangement = Arrangement.spacedBy(8.dp)
            ) {
                Button(onClick = {
                    val newReview = Review(
                        id = if (reviewId == -1) 0 else reviewId,
                        author = author.ifBlank { "Anonymous" },
                        comment = comment.ifBlank { "No comment" },
                        score = score.toIntOrNull() ?: 0,
                        date = java.time.LocalDateTime.now().toString()
                    )

                    if (reviewId == -1) {
                        viewModel.addReview(newReview)
                    } else {
                        viewModel.updateReview(newReview)
                    }

                    onBackClick()
                }) {
                    Text(if (reviewId == -1) "Save review" else "Update review")
                }

                Button(onClick = {
                    if (reviewId != -1) {
                        showDeleteDialog = true
                    }
                }) {
                    Text("Delete")
                }
            }
        }
    }
}
```

## 4.4 Why this structure

Separating responsibilities:

- `ReviewListScreen` is focused on displaying the list
- `ReviewFormScreen` handles entering or editing the review data
- `ReviewNavHost` decides which screen to show
- `ReviewViewModel` still manages the state and API calls
- the repository remains responsible for network access

The navigation host creates one `ReviewViewModel` and passes that same instance to both screens. This keeps the list synchronized after a review is added, updated, or deleted.

`ReviewListScreen` uses `LaunchedEffect(viewModel)` to request the list when the screen becomes active. Without that call, `_reviews` keeps its initial empty value and the UI shows "No reviews available yet."

## 4.5 Connect the app to navigation

In `MainActivity.kt`, replace the single-screen example with the nav host:

```kotlin
import com.example.reviewsapp.ui.navigation.ReviewNavHost
//..

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContent {
            ReviewsAppTheme {
                ReviewNavHost()
            }
        }
    }
}
```

## 4.6 Final app flow

```text
App opens -> ReviewListScreen
Load reviews -> list appears
Tap on a review -> ReviewFormScreen in edit mode
Tap Add review -> ReviewFormScreen in create mode
Save or update -> close form -> return to list
```

