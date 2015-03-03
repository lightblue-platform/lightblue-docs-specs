# Metadata Notes
A place to capture all notes regarding lightblue metadata for RDBMS.

* [entityInfo](#entityInfo)
    * [datastore](#datastore)
* [schema](#schema)
    * [tables](#tables)
    * [primary key](#primary-key)
    * [foreign keys](#foreign-keys)
    * [field definition](#field-definition)
    * [array relationships](#array-relationships)
    * [object relationships](#object-relationships)

# entityInfo

## datastore
In the datastore configuration we reference a datasource by name.  This is container specific, but likely is just a JNDI name.  Additionally, because there are differences between vendors a "dialect" must be specified.

```json
{
    "entityInfo": {
        "datastore": {
            "backend": "rdbms",
            "datasource": "java:/my_datasource",
            "dialect": "oracle"
        },
        "name": "myMetadata"
    },
    "schema": { }
}
```

# schema
All RDBMS extensions in the schema will be in an object called `"rdbms"`.

## tables
Schema defines the tables used in the entity mapping. If there is only one table, fields don't need to explicitly declare table names in column mappings.


```json
{
    "entityInfo": { },
    "schema": {
        "rdbms": {
            "tables": [
                {
                   "table" : "my_default_table",
                    ...
                },
                ...
             ]
        }
    }
}
```

## primary key
This defines the primary keys for each table used in the mapping. Primary keys can include columns that are not mapped in entity metadata. 


```json
{
    "entityInfo": { },
    "schema": {
        "rdbms": { 
            "tables" : [
              {
                "table": "my_default_table",
                "primaryKey": ["id","foo","bar"]
              }
            ]
        }
    }
}
```

## foreign keys
Foreign keys are required to generate join statements, In the structure outlined here, the columns in the remote table map to the referenced table and the two array lenghts must match.  A flag `notNull` indicates if the FK value is optional.

```json
{
    "entityInfo": { },
    "schema": {
        "rdbms": {
           "tables" : [
             {
              "table": "my_default_table",
              "primaryKey": ["id"]
              "foreignKeys": [
                  {
                      "table": "some_other_table",
                      "columns": ["other_id"],
                      "notNull": false,
                      "references": ["id"]
                  }
              ]
           }
        }
    }
}
```

## Default Procedures

Default insert/update/delete procedures can be defined for
tables. Unless specified, those defaults can be used to operate on
those tables:

e.g.

Declaration:
```
{ table : BASE,
  insert: [    { sql : { sql: "select seq.next from dual", resultbinding: [ _id ] } },
               { insert_row : { table: BASE,
                                columns: [ cols ] } } ],
  update: [ ... ],
  delete: [ ... ]
}
```

Usage:
...
 { insert_row : { table: BASE } },
...

When declared and used as above, the default_insert would be replaced
with the insert script for table BASE.

### Default identification

We can define a default WHERE clause to identify a row in the database. This defaults to a WHERE clause using the primary keys of the table. 

Declaration:
```
{ table: BASE,
  identify: { where:"parent_id=? and value=?", bindings:[{field:$parent._id},{field:value} ] }
}
```
The 'identify' declaration then assumed whenever a row needs to be identified, and the WHERE clause is not specified.

```
{ update_row: { table:BASE } }
```


## field definition
Each field should be mapped to a column in a table.  Each column can have a function associated with it to manipulate modified data and for data retrieval.  Properties in `"rdbms"` are:
* `table` - the table for the column
    * if not specified the `table` defined at the root of the schema is used
    * if not specified and `table` is not defined at root of schema metadata persistance must fail
* `column` - the column in the table
    * if not specified the column is assumed to be the metadata field name
* `writeFilter` - a function applied when writing data, takes one argument `?`
    * for example: `"writeFilter": "to_upper(?)`
    * default value is '?'
* `readFilter` - a function applied when reading data, takes one argument `?`
    * for example: `"readFilter": "to_char(?, 'YYYYMMDD')"`
    * default value is '?'

The '?' denotes a bind value; the actual value of the field will replace '?' when statements are executed. Because of this, all read/write filters must contain exactly one '?'.

Example:
```json
{
    "entityInfo": { },
    "schema": {
        "name": "myMetadata",
        "status": {
            "value": "active"
        },
        "version": {
            "value": "0.1.0"
        },
        "fields": {
            "something": {
                "type": "string",
                "rdbms": {
                    "table": "my_table",
                    "column": "my_something",
                    "writeFilter": "to_upper(?)",
                    "readFilter": "to_lower(?)"
                }
            }
        }
    }
}
```

## array relationships
Relationships from an array to the containing object needs to support cases when foreign key columns are not mapped to fields in the metadata.


Example:
```json
{
    "entityInfo": { },
    "schema": {
        "rdbms": {
           "tables" : [
               { 
                  "table" : "my_table",
                  "primaryKey": ["id"],
                  "foreignKeys": [
                     {
                        "table": "some_other_table",
                        "columns": ["other_id"]
                     }
                   ]
                },
                {
                   "table" : "some_other_table",
                   "primaryKey" : ["some_other_id" ],
                }
           ]
        },
        "fields": {
            "a": {
                "type": "string",
                "rdbms": {
                    "table": "my_table",
                    "column": "a_value"
                }
            },
            "arrayStringWithoutPk": {
                "type": "array",
                "items": {
                    "type": "string",
                    "constraints": {
                        "maxLength": 256
                    },
                    "rdbms": {
                        "table": "some_other_table",
                        "column": "string_value"
                    }
                }
            }
        }
    }
}
```

## object relationships
Similar to arrays, objects also will need FK relationships.

Example:
```json
{
    "entityInfo": { },
    "schema": {
        "rdbms": {
           "tables" : [
               { 
                  "table" : "my_table",
                  "primaryKey": ["id"],
                  "foreignKeys": [
                     {
                        "table": "some_other_table",
                        "columns": ["other_id"]
                     }
                   ]
                },
                {
                   "table" : "some_other_table",
                   "primaryKey" : ["some_other_id" ],
                }
           ]
        },
        "fields": {
            "a": {
                "type": "string",
                "rdbms": {
                    "table": "my_table",
                    "column": "a_value"
                }
            },
            "something": {
                "type": "object",
                "fields": {
                    "type": "string",
                    "constraints": {
                        "maxLength": 256
                    },
                    "rdbms": {
                        "table": "some_other_table",
                        "column": "string_value"
                    }
                }
            }
        }
    }
}
```
