# Scenario: Array - PK
This example uses just a single field as examples in each table.  Details for simple data type mappings are captured in the "Simple" scenario and in the "Field Mappings" sections.

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
