# Delete data from Firestore

## delete documents

use `delete()` method

```kotlin
db.collection("cities").document("DC")
    .delete()
    .addOnSuccessListener { Log.d(TAG, "DocumentSnapshot successfully deleted!") }
    .addOnFailureListener { e -> Log.w(TAG, "Error deleting document", e) }
```

When you delete a document, Cloud Firestore does not automatically delete the documents within its subcollections. You can still access the subcollection documents by reference. For example, you can access the document at path /mycoll/mydoc/mysubcoll/mysubdoc even if you delete the ancestor document at /mycoll/mydoc.

## delete field

use `FieldValue.delete()`

```kotlin
val docRef = db.collection("cities").document("BJ")

// Remove the 'capital' field from the document
val updates = hashMapOf<String, Any>(
    "capital" to FieldValue.delete(),
)

docRef.update(updates).addOnCompleteListener { }
```

## delete collections

To delete an entire collection or subcollection in Cloud Firestore, retrieve (read) all the documents within the collection or subcollection and delete them. This process incurs both read and delete costs.

 Deleting collections from an Android client is not recommended.
