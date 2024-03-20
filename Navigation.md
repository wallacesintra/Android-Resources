# Navigation

## **Setup**

build.gradle

```kotlin
dependencies {
    val nav_version = "2.7.7"

    implementation("androidx.navigation:navigation-compose:$nav_version")
}
```

to implement navigation, you have to implement a navigation host, graph and control.

*Host* - UI element that contains the current navigation destination
*Graph* - a data structure that defines all the navigation destiination within the app and how they are connected.
*Conttoller* - the central coordination for managing navigation between destinations. It offers methods for navigating between destinations, handling deep links, managing the back stack...

## **Creating a NavController**

use *NavController* class. It tracks which destinations the user has visited, and allows the user to move between destination.

```kotlin
val navController = rememberNavController()
```

## **Create a NavHost**

use NavHost to create a navigation graph.

```kotlin
val navController = rememberNavController()

NavHost(navController = navController, startDestination = "profile") {
    composable("profile") { Profile( /* ... */ ) }
    composable("friendslist") { FriendsList( /* ... */ ) }
    // Add more destinations similarly.
}
```

example:

```kotlin
// Define the Profile composable.
@Composable
fun Profile(onNavigateToFriendsList: () -> Unit) {
  Text("Profile")
  Button(onClick = { onNavigateToFriendsList() }) {
    Text("Go to Friends List")
  }
}

// Define the FriendsList composable.
@Composable
fun FriendsList(onNavigateToProfile: () -> Unit) {
  Text("Friends List")
  Button(onClick = { onNavigateToProfile() }) {
    Text("Go to Profile")
  }
}

// Define the MyApp composable, including the `NavController` and `NavHost`.
@Composable
fun MyApp() {
  val navController = rememberNavController()
  NavHost(navController, startDestination = "profile") {
    composable("profile") { Profile(onNavigateToFriendsList = { navController.navigate("friendslist") }) }
    composable("friendslist") { FriendsList(onNavigateToProfile = { navController.navigate("profile") }) }
  }
}
```

## **Navigate to a composable**

## intergration with the bottom nav bar

use BottomNavigation and BottomNavigation components.
add

```kotlin
dependencies {
    implementation("androidx.compose.material:material:1.6.3")
}

android {
    buildFeatures {
        compose = true
    }

    composeOptions {
        kotlinCompilerExtensionVersion = "1.5.10"
    }

    kotlinOptions {
        jvmTarget = "1.8"
    }
}
```
