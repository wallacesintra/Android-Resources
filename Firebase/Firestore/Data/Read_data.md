# Get data with Firestore

## initialize cloud firestore

```kotlin
// Access a Cloud Firestore instance from your Activity
val db = Firebase.firestore
```

## Example data

```kotlin
val cities = db.collection("cities")

val data1 = hashMapOf(
    "name" to "San Francisco",
    "state" to "CA",
    "country" to "USA",
    "capital" to false,
    "population" to 860000,
    "regions" to listOf("west_coast", "norcal"),
)
cities.document("SF").set(data1)

val data2 = hashMapOf(
    "name" to "Los Angeles",
    "state" to "CA",
    "country" to "USA",
    "capital" to false,
    "population" to 3900000,
    "regions" to listOf("west_coast", "socal"),
)
cities.document("LA").set(data2)

val data3 = hashMapOf(
    "name" to "Washington D.C.",
    "state" to null,
    "country" to "USA",
    "capital" to true,
    "population" to 680000,
    "regions" to listOf("east_coast"),
)
cities.document("DC").set(data3)

val data4 = hashMapOf(
    "name" to "Tokyo",
    "state" to null,
    "country" to "Japan",
    "capital" to true,
    "population" to 9000000,
    "regions" to listOf("kanto", "honshu"),
)
cities.document("TOK").set(data4)

val data5 = hashMapOf(
    "name" to "Beijing",
    "state" to null,
    "country" to "China",
    "capital" to true,
    "population" to 21500000,
    "regions" to listOf("jingjinji", "hebei"),
)
cities.document("BJ").set(data5)
```

## Get a document

use `get()`

```kotlin
val docRef = db.collection("cities").document("SF")
docRef.get()
    .addOnSuccessListener { document ->
        if (document != null) {
            Log.d(TAG, "DocumentSnapshot data: ${document.data}")
        } else {
            Log.d(TAG, "No such document")
        }
    }
    .addOnFailureListener { exception ->
        Log.d(TAG, "get failed with ", exception)
    }
```

### Source Options

can set the source option to control how a get call uses the offline cache.

You can specify the source option in a get() call to change the default behavior. You can fetch from only the database and ignore the offline cache, or you can fetch from only the offline cache:

```kotlin
val docRef = db.collection("cities").document("SF")

// Source can be CACHE, SERVER, or DEFAULT.
val source = Source.CACHE

// Get the document, forcing the SDK to use the offline cache
docRef.get(source).addOnCompleteListener { task ->
    if (task.isSuccessful) {
        // Document found in the offline cache
        val document = task.result
        Log.d(TAG, "Cached document data: ${document?.data}")
    } else {
        Log.d(TAG, "Cached get failed: ", task.exception)
    }
}
```

### Custom objects

```kotlin
val docRef = db.collection("cities").document("BJ")
docRef.get().addOnSuccessListener { documentSnapshot ->
    val city = documentSnapshot.toObject<City>()
}
```

## Get multiple documents from a collection

can use `whereEqualTo()` to query for all of the documents that meet a certain condition, then use `get()` to retrieve the results:

```kotlin
db.collection("cities")
    .whereEqualTo("capital", true)
    .get()
    .addOnSuccessListener { documents ->
        for (document in documents) {
            Log.d(TAG, "${document.id} => ${document.data}")
        }
    }
    .addOnFailureListener { exception ->
        Log.w(TAG, "Error getting documents: ", exception)
    }
```

By default, Cloud Firestore retrieves all documents that satisfy the query in ascending order by document ID, but you can order and limit the data returned.

### Get all documents in a collection

```kotlin
db.collection("cities")
    .get()
    .addOnSuccessListener { result ->
        for (document in result) {
            Log.d(TAG, "${document.id} => ${document.data}")
        }
    }
    .addOnFailureListener { exception ->
        Log.d(TAG, "Error getting documents: ", exception)
    }
```
