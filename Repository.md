# **Repository**

a repository class:

* exposes data to rest of the app.
* centralized changes to data.
* resolves conflicts between multiple data sources.
* abstract sources of data from the rest of the app.
* contains business logic.

## Create a data layer

naming repository classes: type of data + Repository.
eg MarsPhotosRepository.

### Create repository

1. create a package called data.
2. in the package create a interface and name it the name of the repository.
3. in the interface:

    ```kotlin
    interface MarsPhotosRepository {
        suspend fun getMarsPhotos(): List<MarsPhoto>
    }

    ```

4. create a class to implement the interface.

    ```kotlin
    class NetworkMarsPhotosRepository(): MarsPhotosRepository {
        override suspend fun getMarsPhotos(): List<MarsPhoto> {
            return MarsApi.retrofitService.getPhotos()
        }
    }
    ```

5. in the viewmodel file:

    ```kotlin
    val marsPhotosRepository = NetworkMarsPhotosRepository()
    val listResult = marsPhotosRepository.getMarsPhotos()
    ```

## Dependency injection

Dependency, when a class needs another class to function.

**Dependency Injection** is when a dependency is provided at runtime instead of being hardcoded into the calling class.

A **container** is an object that contains the dependencies that the app requires.

### create an Application container

1. in the data package, select New > Kotlin Class/File.
2. select Interface and enter Container name eg : AppContainer as the name of the interface.
3. inside the interface:

    ```kotlin
    import retrofit2.Retrofit
    import com.jakewharton.retrofit2.converter.kotlinx.serialization.asConverterFactory
    import kotlinx.serialization.json.Json
    import okhttp3.MediaType.Companion.toMediaType

    interface AppContainer {
        val marsPhotosRepository: MarsPhotosRepository
    }

    class DefaultAppContainer : AppContainer {
        private val baseUrl = "https://android-kotlin-fun-mars-server.appspot.com"

         /**
        * Use the Retrofit builder to build a retrofit object using a kotlinx.serialization converter
        */

        private val retrofit = Retrofit.Builder()
            .addConverterFactory(Json.asConverterFactory("application/json".toMediaType()))
            .baseUrl(baseUrl)
            .build()
        
        private val retrofitService: MarsApiService by lazy {
            retrofit.create(MarsApiService::class.java)
        }
    }
    ```

## Attach application container to the app

connect the application object to the application container.

1. in the main package, select New > Kotlin Class/File.

2. In the dialog, enter MarsPhotosApplication. This class inherits from the application object, so you need to add it to the class declaration.

    ```kotlin
    import android.app.Application
    import com.example.marsphotos.data.AppContainer
    import com.example.marsphotos.data.DefaultAppContainer

    class MarsPhotosApplication : Application() {
        lateinit var container: AppContainer
        override fun onCreate() {
            super.onCreate()
            container = DefaultAppContainer()
        }
    }
    ```

3. update the Android manifest, open manifests/AndroidManifest.xml

4. In the application section, add the android:name attribute with a value of application class name ".MarsPhotosApplication".

    ```xml
    <application
        android:name=".MarsPhotosApplication"
        android:allowBackup="true"
        ...
    </application>
    ```

## Add repository to ViewModel

1. in the view model file, in the class declaration of the ViewModel, add a private constructor parameter of type of the repository.

    ```kotlin

    class MarsViewModel(private val marsPhotosRepository: MarsPhotosRepository) {}
    ```

2. implement a ViewModelProvider.Factory object, because ViewModel dont allow to pass values in the constructor when created.

    ```kotlin
    import androidx.lifecycle.ViewModelProvider
    import androidx.lifecycle.ViewModelProvider.AndroidViewModelFactory.Companion.APPLICATION_KEY
    import androidx.lifecycle.viewModelScope
    import androidx.lifecycle.viewmodel.initializer
    import androidx.lifecycle.viewmodel.viewModelFactory
    import com.example.marsphotos.MarsPhotosApplication

    companion object {
        val Factory: ViewModelProvider.Factory = viewModelFactory {
            initializer {
                val application = (this[APPLICATION_KEY] as MarsPhotosApplication)
                val marsPhotosRepository = application.container.marsPhotosRepository
                MarsViewModel(marsPhotosRepository = marsPhotosRepository)
            }
        }
    }
    ```

3. in the composable file,

    ```kotlin
    Surface(
        //...
    ) {
        val marsViewModel: MarsViewModel = viewModel(factory = MarsViewModel.Factory)
    }
    ```
