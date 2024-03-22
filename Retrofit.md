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

## Androids Permission - Internet Premission

1. Open  manifests/AndroidManifest.xml. add the following before the <application> tag:

```kotlin
<uses-permission android:name="android.permission.INTERNET" />
```

## Parse a JSON response with kotlinx.serialization

*Serialization* - process of converting data used by an application to a format that can be transferred over a network.

*Deserialization* - process of reading data from external source and converting it into a runtime object.

[kotlinx.serialization] - has a set of libraries that a JSON string into kotlin objects.

### add kotlinx.serialization library dependencies

* open build.gradle.kts (Module :app)
* in the plugin block, add kotlinx serialization plugin.

```kotlin
id("org.jetbrains.kotlin.plugin.serialization") version "1.8.10"

```

* in the dependencies, add:

```kotlin
// Kotlin serialization 
implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:1.5.1")

// Retrofit with Kotlin serialization Converter

implementation("com.jakewharton.retrofit:retrofit2-kotlinx-serialization-converter:1.0.0")
implementation("com.squareup.okhttp3:okhttp:4.11.0")
```

### create a data class that will store the parsed results

kotlinx.serialization parses this JSON data and converts it into Kotlin objects. To do this, kotlinx.serialization needs to have a Kotlin data class to store the parsed results.

create file of the data class

e.g:

```kotlin
import kotlinx.serialization.Serializable
import kotlinx.serialization.SerialName


@Serializable
data class MarsPhoto(
    val id: String,
    @SerialName(value = "img_src") 
    val imgSrc: String
)
```

### API Services

```kotlin
import com.jakewharton.retrofit2.converter.kotlinx.serialization.asConverterFactory
import kotlinx.serialization.json.Json
import okhttp3.MediaType

private val retrofit = Retrofit.Builder()
        .addConverterFactory(Json.asConverterFactory("application/json".toMediaType()))
        .baseUrl(BASE_URL)
        .build()
```
