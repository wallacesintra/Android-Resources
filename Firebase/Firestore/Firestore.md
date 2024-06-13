# Firestore

## dependencies

```kotlin
dependencies {
    // Import the BoM for the Firebase platform
    implementation(platform("com.google.firebase:firebase-bom:33.1.0"))

    // Declare the dependency for the Cloud Firestore library
    // When using the BoM, you don't specify versions in Firebase library dependencies
    implementation("com.google.firebase:firebase-firestore")
}

```

## Initialize Cloud Firestore

```kotlin
// Access a Cloud Firestore instance from your Activity
val db: FirebaseFirestore = Firebase.firestore
```

## Add data

firestore store data in Documents, that are stored in Collections.

Cloud firestore creates collections and documents implicitly the first time you add data to the document.

```kotlin
// Create a new user with a first and last name
val user = hashMapOf(
    "first" to "Ada",
    "last" to "Lovelace",
    "born" to 1815,
)

// Add a new document with a generated ID
db.collection("users")
    .add(user)
    .addOnSuccessListener { documentReference ->
        Log.d(TAG, "DocumentSnapshot added with ID: ${documentReference.id}")
    }
    .addOnFailureListener { e ->
        Log.w(TAG, "Error adding document", e)
    }
```

Documents in a collection can contain different sets of information

```kotlin
// Create a new user with a first, middle, and last name
val user = hashMapOf(
    "first" to "Alan",
    "middle" to "Mathison",
    "last" to "Turing",
    "born" to 1912,
)

// Add a new document with a generated ID
db.collection("users")
    .add(user)
    .addOnSuccessListener { documentReference ->
        Log.d(TAG, "DocumentSnapshot added with ID: ${documentReference.id}")
    }
    .addOnFailureListener { e ->
        Log.w(TAG, "Error adding document", e)
    }
```

## read data

use `get` method to retrieve the entire collection

```kotlin
db.collection("users")
    .get()
    .addOnSuccessListener { result ->
        for (document in result) {
            Log.d(TAG, "${document.id} => ${document.data}")
        }
    }
    .addOnFailureListener { exception ->
        Log.w(TAG, "Error getting documents.", exception)
    }
```
