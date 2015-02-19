# Scenario: Generate PK
This is a varient of the "Array - No PK" scenario where the ID column is generated with a lightblue "uid" data type.  The point is to show how generated foreign keys are handled.

## Schema: Oracle
```sql
Create Table BASE (
  ID VARCHAR2(50) NOT NULL,
  A_FIELD VARCHAR2(256),
  PRIMARY KEY (ID)
);

Create Table ARRAY_STRING_WITHOUT_PK (
  BASE_ID VARCHAR2(50) NOT NULL,
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
        "fields": {
            "id": {
                "type": "uid",
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
