# Scenario: 2D Array

A simple mapping of multiple RDBMS tables to a single lightblue metadata that has a 2d array (array in an array).  This example uses just a single field as examples in each table.  Details for simple data type mappings are captured in the "Simple" scenario and in the "Field Mappings" sections.

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
```

## Schema: lightblue
```json
```
