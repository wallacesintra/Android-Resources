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

to link the items in a bottom navigation bar to routes in the navigation graph. it is recommended to define a sealed class, such as Screen that contains the route and String resources ID for the destinations.

```kotlin
sealed class Screen(val route: String, @StringRes val resourceId: Int){
    object Profile: Screen("profile", R.string.profile)
    object FriendsList: Screen("friendslist", R.string.friends_list)
}
```

then place those items in a list that can be used by the BottomNavigationItem:

```kotlin
val items = listOf(
    Screen.Profile,
    Screen.FriendsList,
)
```

In the BottomNavigation composable, get the current NavBackStackEntry using the currentBackStackEntryAsState() function.
This entry gives you access to the current NavDestination. The selected state of each BottomNavigationItem can then be determined by comparing the item's route with the route of the current destination and its parent destinations to handle cases when you are using nested navigation using the NavDestination hierarchy.

The item's route is also used to connect the onClick lambda to a call to navigate so that tapping on the item navigates to that item. By using the saveState and restoreState flags, the state and back stack of that item is correctly saved and restored as you swap between bottom navigation items.

```kotlin
val navController = rememberNavController()
Scaffold(
    bottomBar = {
        BottomNavigation {
            val navBackStackEntry by navController.currentBackStackEntryAsState()
            val currentDestination = navBackStackEntry ?.destination

            items.forEach { screen ->
                BottomNavigationItem(
                    icon = { Icon(Icons.Filled.Favorite, contentDescription = null)},
                    label = { Text(stringResource(screen.resourceId))},
                    selected = currentDestination ?.hierarchy?.any {it.route == screen.route} == true,
                    onClick = {
                        navController.navigate(screen.route) {
                            // pop up to start destination to the graph to avoid building up a large stack of destinations on the back stack as users select items
                            popUpTo(navController.graph.findStartDestination().id) {
                                saveState = true
                            }
                            // avoid multiple copies of the same destination when
                            // reselecting the same item
                            launchSingleTop = true
                            // restore state when reselecting a previously selected item
                            restoreState = true
                        }
                    }
                )
            }
        }
    }
){ innerPadding ->
    NavHost(navController, startDestination = Screen.Profile.route, Modifier.padding(innerPadding))
    {
        composable(Screen.Profile.route) { Profile(navController) }
        composable(Screen.FriendsList.route) { FriendsList(navController) }
    }
}
```
