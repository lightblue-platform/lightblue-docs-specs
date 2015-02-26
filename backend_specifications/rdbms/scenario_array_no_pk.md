# Scenario: Array - No PK
This example uses just a single field as examples in each table.  Details for simple data type mappings are captured in the "Simple" scenario and in the "Field Mappings" sections.

**Goal**: Show how an array of simple types works when the array values do not have a primary key.

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
Create Table BASE (
  ID NUMBER(19) NOT NULL,
  A_FIELD VARCHAR2(256),
  PRIMARY KEY (ID)
);

Create Table ARRAY_STRING_WITHOUT_PK (
  BASE_ID NUMBER(19) NOT NULL,
  S_FIELD VARCHAR2(256),
  FOREIGN KEY (BASE_ID) REFERENCES BASE(ID)
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
        "name": "arrayNoPk"
    },
    "schema": {
        "name": "arrayNoPk",
        "status": {
            "value": "active"
        },
        "version": {
            "value": "0.1.0"
        },
        "rdbms": {
            "table": "BASE",
            "primaryKeys": [
                {
                    "table": "BASE",
                    "columns": ["ID"]
                }
            ],
            "foreignKeys": [
                {
                    "table": "ARRAY_STRING_WITHOUT_PK",
                    "columns": ["BASE_ID"],
                    "notNull": true,
                    "references": {
                        "table": "BASE",
                        "columns": ["ID"]
                }
            ]
        },
        "fields": {
            "id": {
                "type": "integer",
                "constraints": {
                    "identity": true
                },
                "rdbms": {
                    "table": "BASE",
                    "column": "ID"
                }
            },
            "a": {
                "type": "string",
                "constraints": {
                    "maxLength": 256
                },
                "rdbms": {
                    "table": "BASE",
                    "column": "A_FIELD"
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
                        "table": "ARRAY_STRING_WITHOUT_PK",
                        "column": "S_FIELD"
                    }
                }
            }
        }
    }
}
```

# insert

## lightblue request
```
PUT /data/insert/arrayNoPk/0.1.0
```
```json
{
    "objectType": "arrayNoPk",
    "version": "0.1.0",
    "data": [
        {
            "id": "123456",
            "a": "b",
            "arrayStringWithoutPk": [
                "one",
                "two",
                "three"
            ]
        }
    ],
    "projection": [
        {
            "field": "*",
            "recursive": true,
            "include": true
        }
    ]
}
```
## generated SQL
```sql
INSERT INTO BASE (ID, A_FIELD)
VALUES (123456, 'b');

INSERT INTO ARRAY_STRING_WITHOUT_PK (BASE_ID, S_FIELD)
VALUES (123456, 'one');

INSERT INTO ARRAY_STRING_WITHOUT_PK (BASE_ID, S_FIELD)
VALUES (123456, 'two');

INSERT INTO ARRAY_STRING_WITHOUT_PK (BASE_ID, S_FIELD)
VALUES (123456, 'three');
```

## lightblue response
```json
{
    "status": "complete",
   "modifiedCount": 1,
    "matchCount": 1,
    "processed": [
        {
            "id": "123456",
            "a": "b",
            "arrayStringWithoutPk": [
                "one",
                "two",
                "three"
            ]
        }
    ]
}
```

# update
Append a value to the array.

## lightblue request
```
POST /data/update/arrayNoPk/0.1.0
```
```json
{
    "objectType": "arrayNoPk",
    "version": "0.1.0",
    "query": {
        "field": "id",
        "op": "=",
        "value": "123456"
    },
    "update": {
        "$append": {
            "arrayStringWithoutPk": [
                "four"
            ]
        }
    },
    "projection": [
        {
            "field": "*",
            "recursive": true,
            "include": true
        }
    ]
}
```

## generated SQL
```sql
INSERT INTO ARRAY_STRING_WITHOUT_PK (BASE_ID, S_FIELD)
VALUES (123456, 'four');
```

## lightblue response
```json
{
    "status": "complete",
    "modifiedCount": 1,
    "matchCount": 1,
    "processed": [
        {
            "id": "123456",
            "a": "b",
            "arrayStringWithoutPk": [
                "one",
                "two",
                "three",
                "four"
            ]
        }
    ]
}
```

# save
Change some of array contents.

## lightblue request
```
POST /data/save/arrayNoPk/0.1.0
```
```json
{
    "objectType": "arrayNoPk",
    "version": "0.1.0",
    "data": [
        {
            "id": "123456",
            "a": "b",
            "arrayStringWithoutPk": [
                "one",
                "apple",
                "three",
                "banana"
            ]
        }
    ],
    "projection": [
        {
            "field": "*",
            "recursive": true,
            "include": true
        }
    ]
}
```
## generated SQL
Comments indicate logic lightblue would be handling.
```sql
/* Delete records that should no longer exist */
DELETE FROM ARRAY_STRING_WITHOUT_PK
WHERE BASE_ID=123456
AND S_FIELD NOT IN ('one', 'apple', 'three', 'banana');

/* Find all records that already exist */
SELECT *
FROM ARRAY_STRING_WIHOUT_PK
WHERE BASE_ID=123456;

/* for each record not in the database insert it */
INSERT INTO ARRAY_STRING_WITHOUT_PK (BASE_ID, S_FIELD)
VALUES (123456, 'apple');
INSERT INTO ARRAY_STRING_WITHOUT_PK (BASE_ID, S_FIELD)
VALUES (123456, 'banana');
```

## lightblue response
```json
{
    "status": "complete",
    "modifiedCount": 1,
    "matchCount": 1,
    "processed": [
        {
            "id": "123456",
            "a": "b",
            "arrayStringWithoutPk": [
                "one",
                "apple",
                "three",
                "banana"
            ]
        }
    ]
}
```

# find

## lightblue request
```
POST /data/find/arrayNoPk/0.1.0
```
```json
{
    "objectType": "arrayNoPk",
    "version": "0.1.0",
    "query": {
        "field": "id",
        "op": "=",
        "rvalue": "123456"
    },
    "projection": [
        {
            "field": "*",
            "recursive": true,
            "include": true
        }
    ]
}
```

## generated SQL
```sql
SELECT *
FROM BASE
WHERE ID=123456;

SELECT *
FROM ARRAY_STRING_WITHOUT_PK
WHERE BASE_ID=123456;
```

## lightblue response
```json
{
    "status": "complete",
    "matchCount": 1,
    "processed": [
        {
            "id": "123456",
            "a": "b",
            "arrayStringWithoutPk": [
                "one",
                "apple",
                "three",
                "banana"
            ]
        }
    ]
}
```

# delete

## lightblue request
```
POST /data/delete/arrayNoPk/0.1.0
```
```json
{
    "objectType": "arrayNoPk",
    "version": "0.1.0",
    "query": {
        "field": "id",
        "op": "=",
        "rvalue": "123456"
    }
}
```

## generated SQL
```sql
DELETE FROM ARRAY_STRING_WITHOUT_PK
WHERE BASE_ID=123456;

DELETE FROM BASE
WHERE ID=123456;
```

## lightblue response
```json
{
    "status": "complete",
    "modifiedCount": 1,
    "matchCount": 1
}
```
