# Auth with Firebase

in the app-level gradle file add:

```kotlin
dependencies {
    // Import the BoM for the Firebase platform
    implementation(platform("com.google.firebase:firebase-bom:33.1.0"))

    // Add the dependency for the Firebase Authentication library
    // When using the BoM, you don't specify versions in Firebase library dependencies
    implementation("com.google.firebase:firebase-auth")
}

```

## Check current auth state

declare an instance of FirebaseAuth

```kotlin
private lateinit var auth: FirebaseAuth
```

in the `onCreate()` method, initialize the FirebaseAuth instance

```kotlin
// Initialize Firebase Auth
auth = Firebase.auth
```

in `onStart()` method, check to see if the user is currently signed in

```kotlin
public override fun onStart() {
    super.onStart()
    // Check if user is signed in (non-null) and update UI accordingly.
    val currentUser = auth.currentUser
    if (currentUser != null) {
        reload()
    }
}
```

## Sign up new users

Create a new createAccount method that takes in an email address and password,
use `createUserWithEmailAndPassword` method.

```kotlin
auth.createUserWithEmailAndPassword(email, password)
    .addOnCompleteListener(this) { task ->
        if (task.isSuccessful) {
            // Sign in success, update UI with the signed-in user's information
            Log.d(TAG, "createUserWithEmail:success")
            val user = auth.currentUser
            updateUI(user)
        } else {
            // If sign in fails, display a message to the user.
            Log.w(TAG, "createUserWithEmail:failure", task.exception)
            Toast.makeText(
                baseContext,
                "Authentication failed.",
                Toast.LENGTH_SHORT,
            ).show()
            updateUI(null)
        }
    }
```

add a form to register new users with their email and password and call the method when it is submitted.

## Sign in existing users

create a signIn method that takes in an email and password, validates them and then signs a user with the `signInWithEmailAndPassword` method.

```kotlin
auth.signInWithEmailAndPassword(email, password)
    .addOnCompleteListener(this) { task ->
        if (task.isSuccessful) {
            // Sign in success, update UI with the signed-in user's information
            Log.d(TAG, "signInWithEmail:success")
            val user = auth.currentUser
            updateUI(user)
        } else {
            // If sign in fails, display a message to the user.
            Log.w(TAG, "signInWithEmail:failure", task.exception)
            Toast.makeText(
                baseContext,
                "Authentication failed.",
                Toast.LENGTH_SHORT,
            ).show()
            updateUI(null)
        }
    }
```

## Access user information

If a user has signed in successfully you can get their account data at any point with the getCurrentUser method.

```kotlin
val user = Firebase.auth.currentUser
user?.let {
    // Name, email address, and profile photo Url
    val name = it.displayName
    val email = it.email
    val photoUrl = it.photoUrl

    // Check if user's email is verified
    val emailVerified = it.isEmailVerified

    // The user's ID, unique to the Firebase project. Do NOT use this value to
    // authenticate with your backend server, if you have one. Use
    // FirebaseUser.getIdToken() instead.
    val uid = it.uid
}
```
