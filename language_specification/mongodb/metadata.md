# Metadata

## schema

Schemas for the mongo backend are managed by lightblue.  Therefore it's important to understand how metadata field types are mapped to mongo data types.  Or more specifically to [BSON types](http://docs.mongodb.org/manual/reference/bson-types/).

| metadata  | mongo | notes |
| --------- | ----- | ----- |
| binary    | String| encoding handled by clients|
| boolean   | Boolean | |
| date      |String| format: YYYYMMDD HHmmssSSSS+-ZZZZ|
| integer   | 64-bit integer | |
| double    | Double | |
| biginteger| String | don't allow < > operators during search |
| bigdecimal| String |don't allow < > operators during search |
| string    | String | |
| uid       |String | |
| object | Object | |
| array | Array | |
| reference | N/A | only used for `find` |
