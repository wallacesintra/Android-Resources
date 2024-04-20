# Get the last Known Location

## set up google play services

```gradle
apply plugin: 'com.android.application'

...

dependencies {
    implementation 'com.google.android.gms:play-services-location:21.2.0'
}

```

check if google play services is installed:

```kotlin
import com.google.android.gms.common.ConnectionResult
import com.google.android.gms.common.GoogleApiAvailability

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            Get_locationTheme {
                // A surface container using the 'background' color from the theme
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    val googleApiAvailability = GoogleApiAvailability.getInstance()
                    val resultCode = googleApiAvailability.isGooglePlayServicesAvailable(this)
                    if (resultCode == ConnectionResult.SUCCESS) {
                        // Google Play Services is available
                        Greeting("Google Play Services is available")
                    } else {
                        // Google Play Services is not available
                        Greeting("Google Play Services is not available")
                    }
                }
            }
        }
    }
}
```

## specify app permission

in AndroidManifest.xml add:

```xml
<!--in "<application> tag"-->

<!-- Recommended for Android 9 (API level 28) and lower. -->
<!-- Required for Android 10 (API level 29) and higher. -->
<service
    android:name="MyNavigationService"
    android:foregroundServiceType="location" ... >
    <!-- Any inner elements would go here. -->
</service>

```

also add:

```xml
<manifest ... >
  <!-- Always include this permission -->
  <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />

  <!-- Include only if your app benefits from precise location access. -->
  <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
</manifest>

```

## create location client

in your activity's `onCreate()` method, create an instance of the Fused Location Provider:

```kotlin
private lateinit var fusedLocationClient: FusedLocationProviderClient

override fun onCreate(savedInstanceState: Bundle?) {
    // ...

    fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)
}

```

## get last known Location

can use the fused location provider's `getLastLocation()` method to retrieve the device location.

```kotlin
fusedLocationClient.lastLocation
        .addOnSuccessListener { location : Location? ->
            // Got last known location. In some rare situations this can be null.
        }
```
