# **Retrofit**

add the following dependencies in build.gradle.kts (Module :app)

```kotlin
// Retrofit 
implementation("com.squareup.retrofit2:retrofit:2.9.0")
// Retrofit with Scalar Converter
implementation("com.squareup.retrofit2:converter-scalars:2.9.0")
```

the scalar converter enables retrofit to return the JSON as a string. It tells the Retrofit what to do with the data it gets from the web service.

1. create a network package, name it network.
2. create a file under the network package, eg MarsApiService

* add a const variable that has the base url:

```kotlin
private const val BASE_URL = "https://android-kotlin-fun-mars-server.appspot.com"
```

()

* add a Retrofit builder, it will need base url and a converter factory to build a web services API. Retrofit has a ScalarsConverter that support primitive data types.

call addConverterFactory() on the builder with an instance of ScalarsConverterFactory.

```kotlin
import retrofit2.Retrofit
import retrofit2.converter.scalars.ScalarsConverterFactory


private val retrofit = Retrofit.Builder()
    .addConverterFactory(ScalarsConverterFactory.create())
    .baseUrl(BASE_URL)
    .build()
```

()

* define an interface that defines how Retrofit talks to web server using HTTP requests.

```kotlin
import retrofit2.http.GET

interface MarsApiService {
    @GET("photos")
    suspend fun getPhotos(): String
}
```

@GET annotation - tell the Retrofit it is a GET request and specify an endpoint for the web service method.

* outside the MarsApiService interface declaration, define a public object called MarsApi to initialize the Retrofit.

```kotlin
object MarsApi{
    val retrofitService: MarsApiService by lazy{
        retrofit.create(MarsApiService::class.java)
    }
}
```

* in a viewmodel file.

```kotlin
import androidx.lifecycle.viewModelScope
import kotlinx.coroutines.launch

private fun getMarsPhotos() {
    viewModelScope.launch {
        val listResult = MarsApi.retrofitService.getPhotos()
        marsUiState = listResult
    }
}
```
