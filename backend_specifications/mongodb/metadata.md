# Metadata

## schema

Schemas for the mongo backend are managed by lightblue.  Therefore it's important to understand how metadata field types are mapped to mongo data types.  Or more specifically to [BSON types](http://docs.mongodb.org/manual/reference/bson-types/).

| metadata  | mongo | notes |
| --------- | ----- | ----- |
| binary    | String| encoding handled by clients|
| boolean   | Boolean | |
| date      |String| format: YYYYMMDDTHH:mm:ss.SSS+-ZZZZ (note T is a literal string)|
| integer   | 64-bit integer | |
| double    | Double | |
| biginteger| String | operators < and > not allowed during search |
| bigdecimal| String | operators < and > not allowed during search |
| string    | String | |
| uid       |String |  |
| object | Object | |
| array | Array | |
| reference | N/A | only used for `find` |

Note there is a special case for any field named `_id`.  For these fields, they are mapped to BSON Object Id if the structure matches the BSON [ObjectID](http://docs.mongodb.org/manual/reference/object-id/) type.
