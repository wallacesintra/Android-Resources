# Get realtime updates with Firestore

```kotlin
val docRef = db.collection("cities").document("SF")
docRef.addSnapshotListener { snapshot, e ->
    if (e != null) {
        Log.w(TAG, "Listen failed.", e)
        return@addSnapshotListener
    }

    if (snapshot != null && snapshot.exists()) {
        Log.d(TAG, "Current data: ${snapshot.data}")
    } else {
        Log.d(TAG, "Current data: null")
    }
}
```

## Listen to multiple documents in a collection

```kotlin
db.collection("cities")
    .whereEqualTo("state", "CA")
    .addSnapshotListener { value, e ->
        if (e != null) {
            Log.w(TAG, "Listen failed.", e)
            return@addSnapshotListener
        }

        val cities = ArrayList<String>()
        for (doc in value!!) {
            doc.getString("name")?.let {
                cities.add(it)
            }
        }
        Log.d(TAG, "Current cites in CA: $cities")
    }
```

## View changes between snapshots

```kotlin
db.collection("cities")
    .whereEqualTo("state", "CA")
    .addSnapshotListener { snapshots, e ->
        if (e != null) {
            Log.w(TAG, "listen:error", e)
            return@addSnapshotListener
        }

        for (dc in snapshots!!.documentChanges) {
            when (dc.type) {
                DocumentChange.Type.ADDED -> Log.d(TAG, "New city: ${dc.document.data}")
                DocumentChange.Type.MODIFIED -> Log.d(TAG, "Modified city: ${dc.document.data}")
                DocumentChange.Type.REMOVED -> Log.d(TAG, "Removed city: ${dc.document.data}")
            }
        }
    }
```

## detach a listener

When you are no longer interested in listening to your data, you must detach your listener so that your event callbacks stop getting called. This allows the client to stop using bandwidth to receive updates.

```kotlin
val query = db.collection("cities")
val registration = query.addSnapshotListener { snapshots, e ->
    // ...
}

// ...

// Stop listening to changes
registration.remove()
```

## handling listen errors

```kotlin
db.collection("cities")
    .addSnapshotListener { snapshots, e ->
        if (e != null) {
            Log.w(TAG, "listen:error", e)
            return@addSnapshotListener
        }

        for (dc in snapshots!!.documentChanges) {
            if (dc.type == DocumentChange.Type.ADDED) {
                Log.d(TAG, "New city: ${dc.document.data}")
            }
        }
    }
```
