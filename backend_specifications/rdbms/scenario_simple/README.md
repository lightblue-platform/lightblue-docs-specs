# Scenario: Simple

Single RDBMS table maps to lightblue metadata with only simple data types.

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
