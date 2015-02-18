# Scenario: Arrays
This combines a few array based scenarios:
* mapping of multiple RDBMS tables to a 2d array of objects
* mapping of simple RDBMS table without a PK to an array of simple types (string)
* mapping of simple RDBMS table with a PK to an array of simple types (string)

This example uses just a single field as examples in each table.  Details for simple data type mappings are captured in the "Simple" scenario and in the "Field Mappings" sections.

## Schema: Oracle
```sql
Create Table BASE (
  ID NUMBER(19) NOT NULL,
  A_FIELD VARCHAR2(256),
  PRIMARY KEY (ID)
);

Create Table ARRAY1 (
  ID NUMBER(19) NOT NULL,
  BASE_ID NUMBER(19) NOT NULL,
  B_FIELD VARCHAR2(256),
  PRIMARY KEY (ID),
  FOREIGN KEY (BASE_ID) REFERENCES BASE(ID)
);

Create Table ARRAY2 (
  ID NUMBER(19) NOT NULL,
  ARRAY1_ID NUMBER(19) NOT NULL,
  C_FIELD VARCHAR2(256),
  PRIMARY KEY (ID),
  FOREIGN KEY (ARRAY1_ID) REFERENCES ARRAY1(ID)
);

Create Table ARRAY_STRING_WITHOUT_PK (
  BASE_ID NUMBER(19) NOT NULL,
  S_FIELD VARCHAR2(256),
  FOREIGN KEY (BASE_ID) REFERENCES BASE(ID)
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
Note that the ARRAY1 and ARRAY2 table's ID columns are mapped as identity fields.  The set of identity fields are assumed to uniquely identify array elements at this time.
```json
{
    "entityInfo": {
        "datastore": {
            "backend": "rdbms",
            "datasource": "my_datasource",
            "dialect": "oracle"
        },
        "name": "2dArray"
    },
    "schema": {
        "name": "2dArray",
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
            "array1": {
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
                                "table": "ARRAY1",
                                "column": "ID"
                            }
                        },
                        "b": {
                            "type": "string",
                            "constraints": {
                                "maxLength": 256
                            },
                            "rdbms": {
                                "table": "ARRAY1",
                                "column": "B_FIELD"
                            }
                        },
                        "array2": {
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
                                            "table": "ARRAY2",
                                            "column": "ID"
                                        }
                                    },
                                    "c": {
                                        "type": "string",
                                        "constraints": {
                                            "maxLength": 256
                                        },
                                        "rdbms": {
                                            "table": "ARRAY2",
                                            "column": "C_FIELD"
                                        }
                                    }
                                }
                            }
                        }
                    }
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
