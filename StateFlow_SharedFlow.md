# Stateflow and SharedFlow

## StateFlow

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

The class responsible for updating a MutableStateFlow is the **Producer**
all classes collecting from the **StateFlow** are the consumer.

```kotlin
class LatestNewsActivity: AppCompatActivity() {
    private val latestNewsViewModel = //getViewModel()

    override fun onCreate(savedInstanceState: Bundle?) {
        lifecycleScope.launch {
            repeatOnLifecycle(Lifecycle.State.STARTED){
                latestNewsViewModel.uiState.collect {
                    when (uiState) {
                        is LatestNewsUiState.Success -> showFavoriteNews(uiState.news)
                        is LatestNewsUiState.Error -> showError(uiState.exception)
                    }
                }
            }
        }
    }
}
```

To convert any flow to a StateFlow, use the stateIn intermediate operator.

### **Making cold flows hot using shareIn**

**StateFlow** - hot flow, remains in memory as long as the flow is collected/while any references to it exist from a garbage collection root.

Can turn cold flow hot using the shareIn operator.

Using the callbackFlow created in Kotlin flows as an example, instead of having each collector create a new flow, you can share the data retrieved from Firestore between collectors by using shareIn. You need to pass in the following:

- CoroutineScope that is used to share the flow, the scope should live longer than any consumer to keep the shared flow alive as long as needed.
- the number of items to replay to each new collector.
- the start behavior policy.

```kotlin
class NewsRemoteDataSource(...,
    private val externalScope: CoroutineScope
) {
    val latestNews: Flow<List<ArticleHeadline>> = flow {
        ...
    }.shareIn(
        externalScope,
        replay = 1,
        started = SharingStarted.WhileSubscribed()
    )
}
```

In this example, the latestNews flow replays the last emitted item to a new collector and remains active as long as externalScope is alive and there are active collectors. The SharingStarted.WhileSubscribed() start policy keeps the upstream producer active while there are active subscribers. Other start policies are available, such as SharingStarted.Eagerly to start the producer immediately or SharingStarted.Lazily to start sharing after the first subscriber appears and keep the flow active forever.

## **SharedFlow**

shareIn function returns a SharedFlow.
**SharedFlow** - hot flow that emits values to all consumers that collects from it.

you could use a SharedFlow to send ticks to the rest of the app so that all the content refreshes periodically at the same time. Apart from fetching the latest news, you might also want to refresh the user information section with its favorite topics collection.

a TickHandler exposes a SharedFlow so that other classes know when to refresh its content. As with StateFlow, use a backing property of type MutableSharedFlow in a class to send items to the flow:

```kotlin
class TickHandler(
    private val externalScope: CoroutineScope,
    private val tickIntervalMs: Long = 5000
) {
    //backing property to avoid flow emission from other classes
    private val _tickFlow = MutableSharedFlow<Unit>(replay = 0)
    val tickFlow: SharedFlow<Event<String>> = _tickFlow

    init {
        externalScope.launch {
            while(true) {
                _tickFlow.emit(Unit)
                delay(tickIntervalMs)
            }
        }
    }
}

class NewsRepository(
    ...,
    private val tickHandler: TickHandler,
    private val externalScope: CoroutineScope
) {
    init {
        externalScope.launch {
            //listen for updates
            tickHandler.tickFlow.collect {
                refreshLatestNews()
            }
        }
    }

    suspend fun refreshLatestNews() { ... }
    ...
}
```
