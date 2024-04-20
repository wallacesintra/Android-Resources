# Coil Library

resources: [coil-kt.github.io/coil/](https://coil-kt.github.io/coil/)

## add coil dependency

1. open build.gradle.kts (Module :app)

2. in dependencies:

    ```kotlin
    // Coil
    implementation("io.coil-kt:coil-compose:2.4.0")
    ```

## display the image URL

use AsyncImage composable

```kotlin
AsyncImage(
    model = "https://android.com/sample_image.jpg",
    contentDescription = null
)
```
