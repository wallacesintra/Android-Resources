# Data

## Firestore Data model

NoSQL, document-like database

Each document has key-value pairs, all documents must be stored in a collection.

### Documents

is the unit of storage of firestore.
Each document is identified by a name.

A document representing a user `alovelace` might look like this:

```text
first : "Ada"
last : "Lovelace"
born : 1815
```

For example, you could structure the user's name from the example above with a map, like this:

```text
name :
    first : "Ada"
    last : "Lovelace"
born : 1815
```

### Collection

Is container of documents

eg: having a collection of users that have various users, each rep by a document.

```text
collections_bookmark users

    class alovelace

    first : "Ada"
    last : "Lovelace"
    born : 1815

    class aturing

    first : "Alan"
    last : "Turing"
    born : 1912
```

### References

it is an object that point to a location in the database.

can create a reference whether or not data exists there, and creating a reference does not perform any network operations

```kotlin
val alovelaceDocumentRef = db.collection("users").document("alovelace")

val usersCollectionRef = db.collection("users")

val alovelaceDocumentRef = db.document("users/alovelace")

```

### Hierarchical data

```text
collections_bookmark rooms

    class roomA

    name : "my chat room"

        collections_bookmark messages

            class message1

            from : "alex"
            msg : "Hello World!"

            class message2

            ...

    class roomB

    ...
```

create a reference to a message in the subcollection:

```kotlin
val messageRef = db
    .collection("rooms").document("roomA")
    .collection("messages").document("message1")
```
