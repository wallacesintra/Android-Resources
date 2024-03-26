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
