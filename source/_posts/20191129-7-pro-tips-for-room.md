---
title: 7 Pro-tips for Room
date: 2019-11-29 17:17:17
tags:
  - android
categories: Android
---
## 1. Pre-populate the database

Do you need to add default data to your database, right after it was created or when the database is opened? Use `RoomDatabase#Callback`! Call the `addCallback` method when building your RoomDatabase and override either `onCreate` or `onOpen`.

`onCreate` will be called when the database is created for the first time, after the tables have been created. `onOpen` is called when the database was opened. Since the DAOs can only be accessed once these methods return, we‘re creating a new thread where we’re getting a reference to the database, get the DAO, and then insert the data.

```kotlin
Room.databaseBuilder(context.applicationContext,
        DataDatabase::class.java, "Sample.db")
        // prepopulate the database after onCreate was called
        .addCallback(object : Callback() {
            override fun onCreate(db: SupportSQLiteDatabase) {
                super.onCreate(db)
                // moving to a new thread
                ioThread {
                    getInstance(context).dataDao()
                                        .insert(PREPOPULATE_DATA)
                }
            }
        })
        .build()
```

See a full example [here](https://gist.github.com/florina-muntenescu/697e543652b03d3d2a06703f5d6b44b5).

**Note**: When using the `ioThread` approach, if your app crashes at the first launch, in between database creation and insert, the data will never be inserted.

## 2. Use DAO’s inheritance capability

Do you have multiple tables in your database and find yourself copying the same `Insert`, `Update` and `Delete` methods? DAOs support inheritance, so create a` BaseDao `class, and define your generic `@Insert`, `@Update` and `@Delete` methods there. Have each DAO extend the `BaseDao` and add methods specific to each of them.

```kotlin
interface BaseDao<T> {
    @Insert
    fun insert(vararg obj: T)
}
@Dao
abstract class DataDao : BaseDao<Data>() {
    @Query("SELECT * FROM Data")
    abstract fun getData(): List<Data>
}
```

See more details [here](https://gist.github.com/florina-muntenescu/1c78858f286d196d545c038a71a3e864).

The DAOs have to be interfaces or abstract classes because Room generates their implementation at compile time, including the methods from `BaseDao`.

## 3. Execute queries in transactions with minimal boilerplate code

Annotating a method with `@Transaction` makes sure that all database operations you’re executing in that method will be run inside one transaction. The transaction will fail when an exception is thrown in the method body.

```kotlin
@Dao
abstract class UserDao {

    @Transaction
    open fun updateData(users: List<User>) {
        deleteAllUsers()
        insertAll(users)
    }
    @Insert
    abstract fun insertAll(users: List<User>)
    @Query("DELETE FROM Users")
    abstract fun deleteAllUsers()
}
```

You might want to use the `@Transaction` annotation for `@Query` methods that have a select statement, in the following cases:

- When the result of the query is fairly big. By querying the database in one transaction, you ensure that if the query result doesn’t fit in a single cursor window, it doesn’t get corrupted due to changes in the database between [cursor window swaps](https://medium.com/google-developers/large-database-queries-on-android-cb043ae626e8).
- When the result of the query is a POJO with `@Relation` fields. The fields are queries separately so running them in a single transaction will guarantee consistent results between queries.

`@Delete`, `@Update` and `@Insert` methods that have multiple parameters are automatically run inside a transaction.

## 4. Read only what you need

When you’re querying the database, do you use all the fields you return in your query? Take care of the amount of **memory** used by your app and load only the subset of fields you will end up using. This will also improve the **speed of your queries** by reducing the IO cost. Room will do the mapping between the columns and the object for you.

Consider this complex `User` object:

```kotlin
@Entity(tableName = "users")
data class User(@PrimaryKey
                val id: String,
                val userName: String,
                val firstName: String, 
                val lastName: String,
                val email: String,
                val dateOfBirth: Date, 
                val registrationDate: Date)
```

On some screens we don’t need to display all of this information. So instead, we can create a `UserMinimal` object that holds only the data needed.

```kotlin
data class UserMinimal(val userId: String,
                       val firstName: String, 
                       val lastName: String)
```

In the DAO class, we define the query and select the right columns from the users table.

```kotlin
@Dao
interface UserDao {
    @Query(“SELECT userId, firstName, lastName FROM Users)
    fun getUsersMinimal(): List<UserMinimal>
}
```

## 5. Enforce constraints between entities with foreign keys

Even though Room doesn’t [directly support relations](https://developer.android.com/topic/libraries/architecture/room.html#no-object-references), it allows you to define Foreign Key constraints between entities.

Room has the `@ForeignKey` annotation, part of the `@Entity` annotation, to allow using the [SQLite foreign key](https://sqlite.org/foreignkeys.html) features. It enforces constraints across tables that ensure the relationship is valid when you modify the database. On an entity, define the parent entity to reference, the columns in it and the columns in the current entity.

Consider a `User` and a `Pet` class. The `Pet` has an owner, which is a user id referenced as foreign key.

```kotlin
@Entity(tableName = "pets",
        foreignKeys = arrayOf(
            ForeignKey(entity = User::class,
                       parentColumns = arrayOf("userId"),
                       childColumns = arrayOf("owner"))))
data class Pet(@PrimaryKey val petId: String,
              val name: String,
              val owner: String)
```

Optionally, you can define what action to be taken when the parent entity is deleted or updated in the database. You can choose one of the following: `NO_ACTION`, `RESTRICT`, `SET_NULL`, `SET_DEFAULT` or `CASCADE`, that have same behaviors as in [SQLite](https://sqlite.org/foreignkeys.html#fk_actions).

**Note**: In Room, `SET_DEFAULT` works as `SET_NULL`, as Room does not yet allow setting default values for columns.

## 6. Simplify one-to-many queries via `@Relation`

In the previous`User`-`Pet` example, we can say that we have a **one-to-many relation**: a user can have multiple pets. Let’s say that we want to get a list of users with their pets: `List`.

```kotlin
data class UserAndAllPets (val user: User,
                           val pets: List<Pet> = ArrayList())
```

To do this manually, we would need to implement 2 queries: one to get the list of all users and another one to get the list of pets based on a user id.

```kotlin
@Query(“SELECT * FROM Users”)
public List<User> getUsers();
@Query(“SELECT * FROM Pets where owner = :userId”)
public List<Pet> getPetsForUser(String userId);
```

We would then iterate through the list of users and query the Pets table.

To make this simpler, Room’s `@Relation` annotation automatically fetches related entities. `@Relation` can only be applied to a `List` or `Set` of objects. The `UserAndAllPets` class has to be updated:

```kotlin
class UserAndAllPets {
   @Embedded
   var user: User? = null
   @Relation(parentColumn = “userId”,
             entityColumn = “owner”)
   var pets: List<Pet> = ArrayList()
}
```

In the DAO, we define a single query and Room will query both the `Users` and the `Pets` tables and handle the object mapping.

```kotlin
@Transaction
@Query(“SELECT * FROM Users”)
List<UserAndAllPets> getUsers();
```

## 7. Avoid false positive notifications for observable queries

Let’s say that you want to get a user based on the user id in an observable query:

```kotlin
@Query(“SELECT * FROM Users WHERE userId = :id)
fun getUserById(id: String): LiveData<User>
// or
@Query(“SELECT * FROM Users WHERE userId = :id)
fun getUserById(id: String): Flowable<User>
```

You’ll get a new emission of the `User` object whenever that user updates. But you will also get the same object when other changes (deletes, updates or inserts) occur on the `Users` table that have nothing to do with the `User` you’re interested in, resulting in false positive notifications. Even more, if your query involves multiple tables, you’ll get a new emission whenever something changed in any of them.

Here’s what’s going on behind the scenes:

1. SQLite supports [triggers](https://sqlite.org/lang_createtrigger.html) that fire whenever a `DELETE`, `UPDATE` or `INSERT` happens in a table.
2. Room creates an `InvalidationTracker` that uses `Observers` that track whenever something has changed in the observed tables.
3. Both `LiveData` and `Flowable` queries rely on the `InvalidationTracker.Observer#onInvalidated` notification. When this is received, it triggers a re-query.

Room only knows that the table has been modified but **doesn’t know why** and **what** has changed. Therefore, after the re-query, the result of the query is emitted by the `LiveData` or `Flowable`. Since Room doesn’t hold any data in memory and can’t assume that objects have `equals()`, it can’t tell whether this is the same data or not.

You need to make sure that your DAO **filters emissions** and only reacts to distinct objects.

If the observable query is implemented using `Flowables`, use `Flowable#distinctUntilChanged`.

```kotlin
@Dao
abstract class UserDao : BaseDao<User>() {
/**
* Get a user by id.
* @return the user from the table with a specific id.
*/
@Query(“SELECT * FROM Users WHERE userid = :id”)
protected abstract fun getUserById(id: String): Flowable<User>
fun getDistinctUserById(id: String): 
   Flowable<User> = getUserById(id)
                          .distinctUntilChanged()
}
```

If your query returns a `LiveData`, you can use a `MediatorLiveData` that only allows distinct object emissions from a source.

```kotlin
fun <T> LiveData<T>.getDistinct(): LiveData<T> {
    val distinctLiveData = MediatorLiveData<T>()
    distinctLiveData.addSource(this, object : Observer<T> {
        private var initialized = false
        private var lastObj: T? = null
        override fun onChanged(obj: T?) {
            if (!initialized) {
                initialized = true
                lastObj = obj
                distinctLiveData.postValue(lastObj)
            } else if ((obj == null && lastObj != null) 
                       || obj != lastObj) {
                lastObj = obj
                distinctLiveData.postValue(lastObj)
            }
        }
    })
    return distinctLiveData
}
```

In your DAOs, make method that returns the distinct `LiveData` `public` and the method that queries the database `protected`.

```kotlin
@Dao
abstract class UserDao : BaseDao<User>() {
@Query(“SELECT * FROM Users WHERE userid = :id”)
   protected abstract fun getUserById(id: String): LiveData<User>
fun getDistinctUserById(id: String): 
         LiveData<User> = getUserById(id).getDistinct()
}
```

See more of the code [here](https://gist.github.com/florina-muntenescu/fea9431d0151ce0afd2f5a0b8834a6c7).

**Note**: if you’re returning a list to be displayed, consider using the [Paging Library](https://developer.android.com/topic/libraries/architecture/paging.html) and returning a [LivePagedListBuilder](https://developer.android.com/reference/android/arch/paging/LivePagedListProvider.html) since the library will help with automatically computing the diff between list items and updating your UI.

来源：

 https://medium.com/androiddevelopers/7-pro-tips-for-room-fbadea4bfbd1 

翻译：

 https://baijiahao.baidu.com/s?id=1626702427361302777&wfr=spider&for=pc 