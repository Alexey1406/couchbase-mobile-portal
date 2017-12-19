---
---

## New Database

As the top-level entity in the API, new databases can be created using the `Database` class by passing in a name, configuration, or both. The following example creates a database using the `Database(string name)` method.

```csharp
var database = new Database("my-database");
```

Just as before, the database will be created in a default location. Alternatively, the `Database(string name, DatabaseConfiguration config)` method can be used to provide specific options (the directory to create the database in, encryption key, etc.)

## Migrating from 1.x Databases

Databases that were created with Couchbase Mobile 1.2 or later can be read using the 2.0 API. Upon detecting it is a 1.x database file format, Couchbase Lite will automatically upgrade it to the new format used in 2.0. This feature is currently only available for the default storage type, SQLite (i.e not for ForestDB databases).

## Finding a Database File

[//]:

Where a database goes by default depends on the platform it is running on.  Here are the defaults for each platform:

- .NET Core: `Path.Combine(AppContext.BaseDirectory, "CouchbaseLite")` (unless the app context is altered \[e.g. by XUnit\], this will be the same directory as the output binary)
- UWP: `Windows.Storage.ApplicationData.Current.LocalFolder.Path` (Inside the installed app sandbox.  Note that this sandbox gets deleted sometimes when debugging from inside Visual Studio when the app is shutdown)
- Xamarin iOS: In a folder named CouchbaseLite inside of `ApplicationSupportDirectory` (this can be retrieved more easily from the simulator using the [SimPholders](https://simpholders.com/3/) utility)
- Xamarin Android: Using the `Context` passed in the `Activate()` method, `Context.FilesDir.AbsolutePath` (database can be retrieved using adb)

## Logging

The log messages are split into different domains (`LogDomains`) which can be tuned to different log levels. The following example enables `Verbose` logging for the `Replicator` and `Query` domains.

[//]:

```c#
Database.SetLogLevel(LogDomain.Replicator, LogLevel.Verbose);
Database.SetLogLevel(LogDomain.Query, LogLevel.Verbose);
```

## Singleton Pattern

The database instance must be used throughout the Couchbase Lite API to Create, Update, Delete and Query documents. Hence, the singleton pattern is useful to create a single instance of the `Database` object. The following example follows the Singleton Pattern in C#.

[//]:

```c#
class DataManager {
  static DataManager SharedInstance = new DataManager()
	
  private DataManager() {
    try {
  	  Database = new Database("dbname")
    } catch(Exception) {
  	  Console.Error.WriteLine("Could not initialize database")
    }
  }
}
```

The database instance can then be access throughout the codebase using the class property: `DataManager.SharedInstance.Database`.

##  Encryption

The following example demonstrates how to create a database with an encryption key (or open an existing one).

[//]: 

```c#
var dbConfig = new DatabaseConfiguration {
    EncryptionKey = new EncryptionKey("secretpassword")
};

Database = new Database("my-database", dbConfig);
```