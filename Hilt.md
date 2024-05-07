# Hilt

Hilt is dependency injection library.

it removes unnecessary boilerplater involved in manual dependencies

in root `build.gradle`:

```kotlin
plugins {
  ...
  id("com.google.dagger.hilt.android") version "2.44" apply false
}

buildscript {
    ...
    ext.hilt_version = '2.40'
    dependencies {
        ...
        classpath "com.google.dagger:hilt-android-gradle-plugin:$hilt_version"
    }
}

```

in `app/build.gradle`:

```kotlin
plugins {
  id 'kotlin-kapt'
  id("com.google.dagger.hilt.android")
}

android {
  ...
}

dependencies {
  implementation("com.google.dagger:hilt-android:2.44")
  kapt("com.google.dagger:hilt-android-compiler:2.44")
}

// Allow references to generated code
kapt {
  correctErrorTypes = true
}

```

hilt uses Java 8 feature, add:

```kotlin
android {
  ...
  compileOptions {
    sourceCompatibility = JavaVersion.VERSION_1_8
    targetCompatibility = JavaVersion.VERSION_1_8
  }
}

```

## Hilt in Application Class

```kotlin
@HiltAndroidApp
class ApplicationName: Application(){

}
```

The application container is the parent container for the app, meaning that other containers can access the dependencies that it provides.
