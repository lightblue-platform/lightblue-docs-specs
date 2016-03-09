# Execution
Execution options are open in each request.  This section describes the supported operations for core and each individual controller implementation.

## Core
None at this time.

## Mongo Controller
Options that are specific to the Mongo Controller.

* writeConcern - The write concern used when executing an insert, save, update, or delete.  Must be a valid WriteConcern value as resolved by the Mongo java driver's [WriteConcern.valueOf(String)](http://api.mongodb.org/java/current/com/mongodb/WriteConcern.html#valueOf-java.lang.String-) method.
* readPreference - The read preference used when executing a find.  Must be a valid ReadPreference value as resolved by the Mongo java driver's [ReadPreference.valueOf(String)](http://api.mongodb.org/java/current/com/mongodb/ReadPreference.html#valueOf-java.lang.String-) method.
* maxQueryTimeMS - The time in milliseconds a find operation can execute on the mongo server.  Translated to a [maxTimeMS](https://docs.mongodb.org/manual/reference/method/cursor.maxTimeMS/#cursor.maxTimeMS) option on find requests (via [DBCursor#maxTime(long,TimeUnit)](http://api.mongodb.org/java/current/com/mongodb/DBCursor.html#maxTime-long-java.util.concurrent.TimeUnit-)).

## LDAP Controller
None at this time.
