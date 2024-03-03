# StateFlow

**StateFlow** - state holder observable flow that emits the current and state updates to its collectors.

The current state value can be also read through its value property.
To update state and send it to flow, assign a new value to value property of the MutableStateFlow class.

StateFlow is good for classes that need to maintain an observable mutable state.

```kotlin
class LatestNewsViewModel(
    private val newsRepository: NewsRepository
) : ViewModel() {
    //backing property to avoid state updates from other classes
    private val _uiState = MutableStateFlow(LatestNewsUiState.Success(emptyList()))
    // the UI collects from this StateFlow to get its state updates
    val uiState: StateFlow<LatestNewsUiState> = _uiState

    init {
        viewModelScope.launch {
            newsRepository.favoriteLatestNews

            .collect { favoriteNews ->
                // Update View with the latest favorite news
                // Writes to the value property of MutableStateFlow,
                // adding a new element to the flow and updating all
                // of its collectors
                _uiState.value = LatestNewsUiState.Success(favoriteNews)

            }
        }
    }
}

// represent different states for the latestNew Screen
sealed class LatestNewSUiState {
    data class Success(val news: List<ArticleHeadline>): LatestNewsUiState()
    data class Error(val exception: Throwable): LatestNewsUiState()
}
```
