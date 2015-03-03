# Scenario: Array - PK
This example uses just a single field as examples in each table.  Details for simple data type mappings are captured in the "Simple" scenario and in the "Field Mappings" sections.

**Goal**: Show how an array of simple types works when the array values have a primary key.

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

Create Table ARRAY_STRING_WITH_PK (
  ID NUMBER(19) NOT NULL,
  BASE_ID NUMBER(19) NOT NULL,
  S_FIELD VARCHAR2(256),
  PRIMARY KEY (ID),
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
        "name": "arrayPk"
    },
    "schema": {
        "name": "arrayPk",
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
                    "table": "ARRAY_STRING_WITH_PK",
                    "columns": ["BASE_ID"],
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
            "arrayStringWithPk": {
                "type": "array",
                "items": {
                    "id": {
                        "type": "integer",
                        "constraints": {
                            "identity": true
                        },
                        "rdbms": {
                            "table": "ARRAY_STRING_WITH_PK",
                            "column": "S_FIELD"
                        }
                    },
                    "s": {
                        "type": "string",
                        "constraints": {
                            "maxLength": 256
                        },
                        "rdbms": {
                            "table": "ARRAY_STRING_WITH_PK",
                            "column": "S_FIELD"
                        }
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
PUT /data/insert/arrayPk/0.1.0
```
```json
{
    "objectType": "arrayPk",
    "version": "0.1.0",
    "data": [
        {
            "id": "123456",
            "a": "b",
            "arrayStringWitPk": [
                { "id":1, s:"one" },
                { "id":2, s:"two" },
                { "id":3, s:"three"}
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

INSERT INTO ARRAY_STRING_WITH_PK (ID, BASE_ID, S_FIELD)
VALUES (1,123456, 'one');

INSERT INTO ARRAY_STRING_WITH_PK (ID, BASE_ID, S_FIELD)
VALUES (2,123456, 'two');

INSERT INTO ARRAY_STRING_WITH_PK (ID, BASE_ID, S_FIELD)
VALUES (3,123456, 'three');
```

## Script

```
 [ { insert_row : { table:BASE, columns: [ $non_null_fields ] } },
   { foreach : { field: arrayStringWithPk, elem: x, 
        do: [  { sql : { sql: "select seq.next from dual", resultbinding: [ id ] } },
               { insert_row : { table:ARRAY_STRING_WITH_PK, 
                        columns: [ { column: base_id, field: $parent_id}, {field: id}, {field:s } ] } } ]
     } 
   }
 ]
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
            "arrayStringWitPk": [
                {1, "one" },
                {2, "two"},
                {3, "three"}
            ]
        }
    ]
}
```

# update
Append a value to the array.

## lightblue request
```
POST /data/update/arrayPk/0.1.0
```
```json
{
    "objectType": "arrayPk",
    "version": "0.1.0",
    "query": {
        "field": "id",
        "op": "=",
        "value": "123456"
    },
    "update": [
        "$append": {
            "arrayStringWithPk": []
        },
        "$set": { "field": "arrayStringWithPk.-1.s", "value": "four" },
        "$set": { "field": "arrayStringWithPk.-1.id","value": 4 }
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
INSERT INTO ARRAY_STRING_WITH_PK (ID,BASE_ID, S_FIELD)
VALUES (4,123456, 'four');
```

## Script

```
[  { update_row : { table: BASE, columns: [ $modified_columns ] } },
   { collection_update : { field : arrayStringWithPk, 
                           table: ARRAY_STRING_WITH_PK, 
                           retrieval: { $select : { from : ARRAY_STRING_WITH_PK, 
                                                    columns : [ $all_columns, { column: base_id } ],
                                                    where : { q: "base_id=?", bindings: [ { field:$parent._id } ] } } },
                           inserted_rows: { insert_row : { table: ARRAY_STRING_WITH_PK, 
                                               columns: [ {field:s}, {field:id}, {column:base_id,field:$parent._id}]} },
                           updated_rows: { update_row: {table: ARRAY_STRING_WITH_PK, columns[$modified_columns],
                                                        where: {q: "base_id=? and id=?", bindings:[{field:$parent._id}, 
                                                                                                   {field:id}]} }},
                           deleted_rows: { delete_row: {table: ARRAY_STRING_WITH_PK,
                                                        where: { q: "base_id=? and id=?", bindings: [{field:$parent._id}, 
                                                                                                     {field:id}]} } } } }
]
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
            "arrayStringWithPk": [
                {1,"one"},
                {2,"two"},
                {3,"three"},
                {4,"four"}
            ]
        }
    ]
}
```

# find

## lightblue request
```
POST /data/find/arrayPk/0.1.0
```
```json
{
    "objectType": "arrayPk",
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
SELECT * FROM BASE A JOIN ARRAY_STRING_WITH_PK B ON A.ID=B.BASE_ID WHERE A.ID=123456
```

## Script

```
{ select : { project: [ $all_columns ],
             join: [ { "A":"BASE"}, {"B":"ARRAY_STRING_WITH_PK"} ],
             where : { sql: "A._ID = ?", bindings: [ _id ]  }
           } }
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
            "arrayStringWithPk": [
                {1, "one"},
                {2,"two"}
                {3,"three"},
                {4,"banana"}
            ]
        }
    ]
}
```

# delete

## lightblue request
```
POST /data/delete/arrayPk/0.1.0
```
```json
{
    "objectType": "arrayPk",
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

## Script
```
[ { delete_row: { table: ARRAY_STRING_WITHOUT_PK, where: { sql: "BASE_ID=?", bindings:[123456]} } },
  { delete_row: { table: BASE, where: { sql: "ID=?", bindings:[123456] } } } ]
```

## lightblue response
```json
{
    "status": "complete",
    "modifiedCount": 1,
    "matchCount": 1
}
```
