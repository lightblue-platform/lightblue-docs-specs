# Metadata Notes
A place to capture all notes regarding lightblue metadata for RDBMS.

* [entityInfo](#entityInfo)
    * [datastore](#datastore)
* [schema](#schema)
    * [default table](#default-table)
    * [primary keys](#primary-keys)
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

## default table
At the schema level, you can optionally set a default table.  This table is used for any fields that don't explicitely set a table.


```json
{
    "entityInfo": { },
    "schema": {
        "rdbms": {
            "table": "my_default_table"
        }
    }
}
```

## primary keys
In some cases the primary key may not be mapped to the metadata.  We still need a way to identify what that column is.  Because there could be many tables mapped to fields on any object within metadata it is necessary to capture information per table independent of field data.  This gives a way to lookup the PK for generating joins.

```json
{
    "entityInfo": { },
    "schema": {
        "rdbms": {
            "table": "my_default_table",
            "primaryKeys": [
                {
                    "table": "my_default_table",
                    "columns": ["id"]
                },
                {
                    "table": "some_other_table",
                    "columns": ["foo", "bar"]
                }
            ]
        }
    }
}
```

## foreign keys
Additionally, foreign keys have the same problem.  In the structure outlined here, the columns in the remote table map to the referenced table and the two array lenghts must match.  A flag `notNull` indicates if the FK value is optional.

```json
{
    "entityInfo": { },
    "schema": {
        "rdbms": {
            "table": "my_default_table",
            "primaryKeys": [
                {
                    "table": "my_default_table",
                    "columns": ["id"]
                }
            ],
            "foreignKeys": [
                {
                    "table": "some_other_table",
                    "columns": ["other_id"],
                    "notNull": false,
                    "references": {
                        "table": "my_default_table",
                        "columns": ["id"]
                }
            ]
        }
    }
}
```

## field definition
Each field should be mapped to a column in a table.  Each column can have a function associated with it to manipulate modified data and for data retrieval.  Properties in `"rdbms"` are:
* `table` - the table for the column
    * if not specified the `table` defined at the root of the schema is used
    * if not specified and `table` is not defined at root of schema metadata persistance must fail
* `column` - the column in the table
    * if not specified the column is assumed to be the metadata field name
* `writeFilter` - a function applied when writing data, takes one argument `$value`
    * for example: `"writeFilter": "to_upper($value)`
* `readFilter` - a function applied when reading data, takes one argument `$value`
    * for example: `"readFilter": "to_char($value, 'YYYYMMDD')"`

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
                    "writeFilter": "to_upper($value)",
                    "readFilter": "to_lower($value)"
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
            "primaryKeys": [
                {
                    "table": "my_table",
                    "columns": ["id"]
                }
            ],
            "foreignKeys": [
                {
                    "table": "some_other_table",
                    "columns": ["other_id"],
                    "references": {
                        "table": "my_table",
                        "columns": ["id"]
                }
            ]
        }
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
            "primaryKeys": [
                {
                    "table": "my_table",
                    "columns": ["id"]
                }
            ],
            "foreignKeys": [
                {
                    "table": "some_other_table",
                    "columns": ["other_id"],
                    "references": {
                        "table": "my_table",
                        "columns": ["id"]
                }
            ]
        }
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
