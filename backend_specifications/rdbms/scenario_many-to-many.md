# Scenario: Many-to-Many
Two tables with a map table between them, resulting in a many-to-many relationship.

**Goal**: Show how a many-to-many relationship could be represented.

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
Create Table TABLE_A (
  ID NUMBER(19) NOT NULL,
  A_FIELD VARCHAR2(256),
  PRIMARY KEY (ID)
);

Create Table TABLE_B (
  ID NUMBER(19) NOT NULL,
  B_FIELD VARCHAR2(256),
  PRIMARY KEY (ID)
);

Create Table MAP_A_B (
  A_ID NUMBER(19) NOT NULL,
  B_ID NUMBER(19) NOT NULL,
  PRIMARY KEY (A_ID, B_ID),
  FOREIGN KEY (A_ID) REFERENCES TABLE_A(ID),
  FOREIGN KEY (B_ID) REFERENCES TABLE_B(ID)
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
        "name": "manyToMany"
    },
    "schema": {
        "name": "manyToMany",
        "status": {
            "value": "active"
        },
        "version": {
            "value": "0.1.0"
        },
        "rdbms": {
            "tables": [
                {
                    "table": "TABLE_A",
                    "primaryKey": [
                        "ID"
                    ]
                },
                {
                    "table": "TABLE_B",
                    "primaryKey": [
                        "ID"
                    ]
                },
                {
                    "table": "MAP_A_B",
                    "primaryKey": [
                        "A_ID",
                        "B_ID"
                    ],
                    "foreignKeys": [
                        {
                            "table": "TABLE_A",
                            "column": "A_ID"
                        },
                        {
                            "table": "TABLE_B",
                            "column": "B_ID"
                        }
                    ]
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
                    "table": "TABLE_A",
                    "column": "ID"
                }
            },
            "a": {
                "type": "string",
                "constraints": {
                    "maxLength": 256
                },
                "rdbms": {
                    "table": "TABLE_A",
                    "column": "A_FIELD"
                }
            },
            "b": {
                "type": "array",
                "items": {
                    "type": "object",
                    "fields": {
                        "id": {
                            "type": "integer",
                            "constraints": {
                                "identity": true
                            },
                            "rdbms": {
                                "table": "TABLE_B",
                                "column": "ID"
                            }
                        },
                        "b": {
                            "type": "string",
                            "constraints": {
                                "maxLength": 256
                            },
                            "rdbms": {
                                "table": "TABLE_B",
                                "column": "B_FIELD"
                            }
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
PUT /data/insert/manyToMany/0.1.0
```
```json
{
    "objectType": "manyToMany",
    "version": "0.1.0",
    "data": [
        {
            "id": "100",
            "a": "foo",
            "b": [
                {
                    "id": "200",
                    "b": "bar"
                },
                {
                    "id": "300",
                    "b": "baz"
                }
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
/*
Request includes fields bound to the following tables:
* TABLE_A
* TABLE_B
*/

/*
TABLE_A has no FK to other tables, insert.
*/
INSERT INTO TABLE_A (ID, A_FIELD)
VALUES (100, 'foo');

/*
TABLE_B has no FK to other tables, insert.
*/
INSERT INTO TABLE_B (ID, B_FIELD)
VALUES (200, 'bar');

INSERT INTO TABLE_B (ID, B_FIELD)
VALUES (300, 'baz');

/*
TABLE_A is defined as part of FK in MAP_A_B.
TABLE_A.ID is part of composite PK in MAP_A_B.
TABLE_B.ID is part of composite PK in MAP_A_B.
TABLE_B is defined as part of FK in MAP_A_B.
All conditions are met to create relationships.
Decision: insert records in MAP_A_B
*/
INSERT INTO MAP_A_B (A_ID, B_ID)
VALUES (100, 200);
INSERT INTO MAP_A_B (A_ID, B_ID)
VALUES (100, 300);
```

## Script
The script below assumes elements of 'b' are looked up, and inserted if not found.
```json
[
    {   "$set": "$tables.table_a", "var":"$document" },
    {   "insert_row" : { "table": "table_a" } },
    {   "foreach": {
            "field": "$document.b",
            "elem": "x",
            "do": [
                { "$set": "result", "valueof":{ "select" : { "table": "table_b",
                                                "project": [ {"column":"base_id"} ],
                                                "where": { "clause": "id=?",
                                                           "bindings": ["$x.id"] } } } },
                { "conditional" : { "test": { "isempty":"$result"}},
                      {"then":  [
                             { "$set":"$tables.TABLE_B", "var":"x" },
                             { "insert_row": {  "table": "TABLE_B" } },
                             { "$set":"$tables.MAP_A_B.a_id", "var":"$document.id" },
                             { "$set":"$tables.MAP_A_B.b_id", "var":"x.id"},
                             { "insert_row": { "table" : "MAP_A_B" } }
                             ]
                      }
                }
                }
            ]
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
            "id": "100",
            "a": "foo",
            "b": [
                {
                    "id": "200",
                    "b": "bar"
                },
                {
                    "id": "300",
                    "b": "baz"
                }
            ]
        }
    ]
}
```
