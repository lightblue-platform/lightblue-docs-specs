# Scenario: Simple
Single RDBMS table maps to lightblue metadata with only simple data types.

**Goal**: Show basic operations with a very simple scenario

**Contents**:
* [Setup](#setup)
* [insert](#insert)
* [update](#update)
* [save](#save)
* [find](#find)
* [delete](#delete)

# Setup

## Schema: Oracle
```sql
Create Table MY_TABLE (
  MY_RAW RAW(1024),
  MY_MLSLABEL MLSLABEL,
  MY_BLOB BLOB,
  MY_BFILE BFILE,
  MY_DATE DATE,
  MY_TIMESTAMP TIMESTAMP,
  MY_NUMBER NUMBER NOT NULL,
  MY_BINARY_FLOAT BINARY_FLOAT,
  MY_BINARY_DOUBLE BINARY_DOUBLE,
  MY_VARCHAR2 VARCHAR2(256),
  MY_NVARCHAR2 NVARCHAR2(256),
  MY_CHAR CHAR(10),
  MY_NCHAR NCHAR(10),
  MY_LONG LONG,
  MY_ROWID ROWID,
  MY_UROWID UROWID,
  MY_CLOB CLOB,
  MY_NCLOB NCLOB,
  MY_XMLTYPE XMLTYPE,
  PRIMARY KEY (MY_NUMBER)
);
```

## Schema: lightblue
```json
{
    "entityInfo": {
        "datastore": {
            "backend": "rdbms",
            "datasource": "my_datasource",
            "dialect": "oracle"
        },
        "name": "myMetadata"
    },
    "schema": {
        "name": "myMetadata",
        "status": {
            "value": "active"
        },
        "version": {
            "value": "0.1.0"
        },
        "fields": {
            "raw": {
                "type": "binary",
                "constraints": {
                    "maxLength": 1024
                },
                "rdbms": {
                    "table": "my_table",
                    "column": "my_raw"
                }
            },
            "mlslabel": {
                "type": "binary",
                "rdbms": {
                    "table": "my_table",
                    "column": "my_mslabel"
                }
            },
            "blob": {
                "type": "binary",
                "rdbms": {
                    "table": "my_table",
                    "column": "my_blob"
                }
            },
            "bfile": {
                "type": "binary",
                "rdbms": {
                    "table": "my_table",
                    "column": "my_bfile"
                }
            },
            "date": {
                "type": "date",
                "rdbms": {
                    "table": "my_table",
                    "column": "my_date"
                }
            },
            "timestamp": {
                "type": "date",
                "rdbms": {
                    "table": "my_table",
                    "column": "my_timestamp"
                }
            },
            "number": {
                "type": "integer",
                "constraints": {
                    "identity": true
                },
                "rdbms": {
                    "table": "my_table",
                    "column": "my_number"
                }
            },
            "binaryFloat": {
                "type": "double",
                "rdbms": {
                    "table": "my_table",
                    "column": "my_binary_float"
                }
            },
            "binaryDouble": {
                "type": "double",
                "rdbms": {
                    "table": "my_table",
                    "column": "my_binary_double"
                }
            },
            "varchar2": {
                "type": "string",
                "constraints": {
                    "maxLength": 256
                },
                "rdbms": {
                    "table": "my_table",
                    "column": "my_varchar2"
                }
            },
            "nvarchar2": {
                "type": "string",
                "constraints": {
                    "maxLength": 256
                },
                "rdbms": {
                    "table": "my_table",
                    "column": "my_nvarchar2"
                }
            },
            "char": {
                "type": "string",
                "constraints": {
                    "maxLength": 10
                },
                "rdbms": {
                    "table": "my_table",
                    "column": "my_char"
                }
            },
            "nchar": {
                "type": "string",
                "constraints": {
                    "maxLength": 10
                },
                "rdbms": {
                    "table": "my_table",
                    "column": "my_nchar"
                }
            },
            "long": {
                "type": "string",
                "rdbms": {
                    "table": "my_table",
                    "column": "my_long"
                }
            },
            "rowid": {
                "type": "string",
                "rdbms": {
                    "table": "my_table",
                    "column": "my_rowid"
                }
            },
            "urowid": {
                "type": "string",
                "rdbms": {
                    "table": "my_table",
                    "column": "my_urowid"
                }
            },
            "clob": {
                "type": "string",
                "rdbms": {
                    "table": "my_table",
                    "column": "my_clob"
                }
            },
            "nclob": {
                "type": "string",
                "rdbms": {
                    "table": "my_table",
                    "column": "my_nclob"
                }
            },
            "xmltype": {
                "type": "string",
                "rdbms": {
                    "table": "my_table",
                    "column": "my_xmltype"
                }
            }
        }
    }
}
```

# insert

## lightblue request
```
PUT /data/insert/myMetadata/0.1.0
```
```json
{
    "objectType": "myMetadata",
    "version": "0.1.0",
    "data": [
        {
            "date": "20150217 1721000000-0500",
            "number": "123456",
            "varchar2": "hi there"
        }
    ],
    "projection": [
        {
            "field": "*",
            "include": true
        }
    ]
}
```
## generated SQL
```sql
INSERT INTO MY_TABLE (MY_DATE, MY_NUMBER, MY_VARCHAR2)
VALUES (to_date('20150217 222100', 'YYYYMMDD HH24MISS'), 123456, 'hi there');
```

## Script

```json
{ insert_row: { table: my_table, columns: [ $non_null_columns ] } }
```

## lightblue response
```json
{
    "status": "complete",
   "modifiedCount": 1,
    "matchCount": 1,
    "processed": [
        {
            "date": "20150217 222100",
            "number": "123456",
            "varchar2": "hi there"
        }
    ]
}
```

# update

## lightblue request
```
POST /data/update/myMetadata/0.1.0
```
```json
{
    "objectType": "myMetadata",
    "version": "0.1.0",
    "query": {
        "field": "number",
        "op": "=",
        "value": "123456"
    },
    "update": {
        "$set": {
            "char": "boo!"
        }
    },
    "projection": [
        {
            "field": "*",
            "include": true
        }
    ]
}
```

## generated SQL
```sql
UPDATE MY_TABLE
SET MY_CHAR='boo!'
WHERE MY_NUMBER = 123456;
```

### Script
```json
{ update_row : { table: my_table, columns: [ $modified_columns ], where: { sql:"my_number=?", bindings: [ 123456] } } }
```

## lightblue response
```json
{
    "status": "complete",
    "modifiedCount": 1,
    "matchCount": 1,
    "processed": [
        {
            "date": "20150217 222100",
            "number": "123456",
            "varchar2": "hi there",
            "char": "boo!"
        }
    ]
}
```

# save

## lightblue request
```
POST /data/save/myMetadata/0.1.0
```
```json
{
    "objectType": "myMetadata",
    "version": "0.1.0",
    "data": [
        {
            "date": "20150217 1721000000-0500",
            "number": "123456",
            "varchar2": "hi there",
            "char": "boo!",
            "long": "A long time ago in a galaxy far, far away...."
        }
    ],
    "projection": [
        {
            "field": "*",
            "include": true
        }
    ]
}
```
## generated SQL
```sql
UPDATE MY_TABLE
SET MY_DATE=to_date('20150217 222100', 'YYYYMMDD HH24MISS'),
MY_VARCHAR2='hi there',
MY_CHAR='boo!',
MY_LONG='A long time ago in a galaxy far, far away....'
WHERE MY_NUMBER = 123456;
```

## lightblue response
```json
{
    "status": "complete",
    "modifiedCount": 1,
    "matchCount": 1,
    "processed": [
        {
            "date": "20150217 222100",
            "number": "123456",
            "varchar2": "hi there",
            "char": "boo!",
            "long": "A long time ago in a galaxy far, far away...."
        }
    ]
}
```

# find

## lightblue request
```
POST /data/find/myMetadata/0.1.0
```
```json
{
    "objectType": "myMetadata",
    "version": "0.1.0",
    "query": {
        "field": "number",
        "op": "=",
        "rvalue": "123456"
    },
    "projection": [
        {
            "field": "*",
            "include": true
        }
    ]
}
```

## generated SQL
```sql
SELECT *
FROM MY_TABLE
WHERE MY_NUMBER=123456;
```

## lightblue response
```json
{
    "status": "complete",
    "matchCount": 1,
    "processed": [
        {
            "date": "20150217 222100",
            "number": "123456",
            "varchar2": "hi there",
            "char": "boo!",
            "long": "A long time ago in a galaxy far, far away...."
        }
    ]
}
```

# delete

## lightblue request
```
POST /data/delete/myMetadata/0.1.0
```
```json
{
    "objectType": "myMetadata",
    "version": "0.1.0",
    "query": {
        "field": "number",
        "op": "=",
        "rvalue": "123456"
    }
}
```

## generated SQL
```sql
DELETE FROM MY_TABLE
WHERE MY_NUMBER=123456;
```

## Script
```json
{ delete_row: { table:my_table, where: { sql: "my_number=?", bindings: [ _123456] } } }
```
##

## lightblue response
```json
{
    "status": "complete",
    "modifiedCount": 1,
    "matchCount": 1
}
```
