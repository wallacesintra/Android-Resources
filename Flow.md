# **Kotlin Flow in Android**

**Flow** - emits multiple sequencial values. example can used to get live updates from a database.
opposed to suspended fun that emit only single value.

**Flow** - conceptually stream of data can be computed asynchronously. value of the same type.

## **Entities invloved in stream of data__

1. **Producer**: produces data, can be asynchronously.
2. **(Optional)Intermediaries**: modify the value emitted.
3. **Consumer**: consumes the value from the stream.

## **Creating a flow**

use flow builder APIs.
flow builder uses emit function to manually emit new values into the stream.

the example :
a data source fetches the latest news automatically at a fixed internal.

```kotlin
class NewsRemoteDataSource(
    private val newsApi: NewsApi,
    private val refreshIntervalMs: Long = 5000
) {
    val latestNews: Flow<List<ArticleHeadline>> = flow {
        while(true) {
            val latestNews = newsApi.fetchLatestNews()
            emit(latestNews) // Emits the result of the request to the flow
            delay(refreshIntervalMs) // Suspends the coroutine for some time
        }
    }
}

// Interface that provides a way to make network requests with suspend functions
interface NewsApi {
    suspend fun fetchLatestNews(): List<ArticleHeadline>
}
```

## **Modifying stream**

Intermediaties uses intermediates operators to modify stream of data without consuming the values.

```kotlin
class NewsRepository(
    private val newsRemoteDataSource: NewsRemoteDataSource,
    private val userData: UserData
) {
    /**
     * Returns the favorite latest news applying transformations on the flow.
     * These operations are lazy and don't trigger the flow. They just transform
     * the current value emitted by the flow at that point in time.
     */
    val favoriteLatestNews: Flow<List<ArticleHeadline>> =
        newsRemoteDataSource.latestNews
            // Intermediate operation to filter the list of favorite topics
            .map { news -> news.filter { userData.isFavoriteTopic(it) } }
            // Intermediate operation to save the latest news in the cache
            .onEach { news -> saveInCache(news) }
}
```

Intermediate operators can be applied one after the other, forming a chain of operations that are executed lazily when an item is emitted into the flow.

## **Collecting from a flow**

use collect - get all value value from a stream.
collect is a suspend function, must be executed within a coroutine.

```kotlin
class LatestNewsViewModel(
    private val newsRepository: NewsRepository
) : ViewModel() {

    init {
        viewModelScope.launch {
            // Trigger the flow and consume its elements using collect
            newsRepository.favoriteLatestNews.collect { favoriteNews ->
                // Update View with the latest favorite news
            }
        }
    }
}
```

Collecting the flow triggers the producer that refreshes the latest news and emits the result of the network request on a fixed interval.

the producer will be active while true loop, the stream of data will be closed when the ViewModel is cleared & viewModelScope is cancelled.

Flow collection can stop if:

- the coroutine that collects is cancelled.
- the producer finishes emitting items.
