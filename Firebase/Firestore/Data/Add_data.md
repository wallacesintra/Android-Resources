# Add and manage data

## Add data to Firestore

### Initialize Firestore

```kotlin
// Access a Cloud Firestore instance from your Activity
val db = Firebase.firestore
```

### set a document

use `set()` method to create or overwrite a single document.

```kotlin
val city = hashMapOf(
    "name" to "Los Angeles",
    "state" to "CA",
    "country" to "USA",
)

db.collection("cities").document("LA")
    .set(city)
    .addOnSuccessListener { Log.d(TAG, "DocumentSnapshot successfully written!") }
    .addOnFailureListener { e -> Log.w(TAG, "Error writing document", e) }
```

if the document does not exist it'll be created.

if it exist its content will be overwritten with the new data, unless you state they should merge:

```kotlin
// Update one field, creating the document if it does not already exist.
val data = hashMapOf("capital" to true)

db.collection("cities").document("BJ")
    .set(data, SetOptions.merge())

```

#### Data types

```kotlin
val docData = hashMapOf(
    "stringExample" to "Hello world!",
    "booleanExample" to true,
    "numberExample" to 3.14159265,
    "dateExample" to Timestamp(Date()),
    "listExample" to arrayListOf(1, 2, 3),
    "nullExample" to null,
)

val nestedData = hashMapOf(
    "a" to 5,
    "b" to true,
)

docData["objectExample"] = nestedData

db.collection("data").document("one")
    .set(docData)
    .addOnSuccessListener { Log.d(TAG, "DocumentSnapshot successfully written!") }
    .addOnFailureListener { e -> Log.w(TAG, "Error writing document", e) }
```

#### Custom objects

```kotlin
data class City(
    val name: String? = null,
    val state: String? = null,
    val country: String? = null,
    @field:JvmField // use this annotation if your Boolean field is prefixed with 'is'
    val isCapital: Boolean? = null,
    val population: Long? = null,
    val regions: List<String>? = null,
)

val city = City(
    "Los Angeles",
    "CA",
    "USA",
    false,
    5000000L,
    listOf("west_coast", "socal"),
)
db.collection("cities").document("LA").set(city)

```

### Add a document

use `set()` to create a document, you must specify an ID for the document to create.

```kotlin
db.collection("cities").document("new-city-id").set(data)
```

use `add()` if you want specify an ID, firestore auto-generate ID for you.

```kotlin
// Add a new document with a generated id.
val data = hashMapOf(
    "name" to "Tokyo",
    "country" to "Japan",
)

db.collection("cities")
    .add(data)
    .addOnSuccessListener { documentReference ->
        Log.d(TAG, "DocumentSnapshot written with ID: ${documentReference.id}")
    }
    .addOnFailureListener { e ->
        Log.w(TAG, "Error adding document", e)
    }

```

create a document reference with an auto-generated ID, then use the reference later. For this use case, you can call doc():

```kotlin
val data = HashMap<String, Any>()

val newCityRef = db.collection("cities").document()

// Later...
newCityRef.set(data)
```

### Update a document

use `update()` to update a document without overwritting the entire document.

```kotlin
val washingtonRef = db.collection("cities").document("DC")

// Set the "isCapital" field of the city 'DC'
washingtonRef
    .update("capital", true)
    .addOnSuccessListener { Log.d(TAG, "DocumentSnapshot successfully updated!") }
    .addOnFailurearrayRemove()
#### Updating fields in a nested objects

```kotlin
// Assume the document contains:
// {
//   name: "Frank",
//   favorites: { food: "Pizza", color: "Blue", subject: "recess" }
//   age: 12
// }
//
// To update age and favorite color:
db.collection("users").document("frank")
    .update(
        mapOf(
            "age" to 13,
            "favorites.color" to "Red",
        ),
    )
```

#### Upating an element in an array

`arrayUnion()`: adds elements to an array but only elements not already present.

`arrayRemove()`: removes all instances of each given element.

```kotlin
val washingtonRef = db.collection("cities").document("DC")

// Atomically add a new region to the "regions" array field.
washingtonRef.update("regions", FieldValue.arrayUnion("greater_virginia"))

// Atomically remove a region from the "regions" array field.
washingtonRef.update("regions", FieldValue.arrayRemove("east_coast"))

```

#### incrementing a numeric value

```kotlin
val washingtonRef = db.collection("cities").document("DC")

// Atomically increment the population of the city by 50.
washingtonRef.update("population", FieldValue.increment(50))

```
