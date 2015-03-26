# SQL Script Language

The SQL script language for the RDBMS backend provides a way to
assemble an algorithm that performs a database operation using
predefined algorithm components. Each script component is a Java class
implementing a particular logic.

A script contains one or more operations:
```
script := operation | [ operation, operation, ... ]
```

An operation has an operation name, and arguments:
``` 
operation := { operationName : { args } }
```

Each operation represents an algorithm that can be invoked to perform
an operation. This algorithm can be the execution of a SQL statement,
or execution of a particular logic. 

An operation returns a value that can be assigned to a variable. 

## Variables
For each different operation, scripts execute with a set of variables accessible to them. These
are the variables that are predefined for each operation.

* Insert
  * $document: The document being inserted.
* Update
  * $document: The new copy of the document. 
  * $olddocument: The unmodified copy of the document. Fields of olddocument are read-only.
* Delete
  * $docid : The unique ID of the document that's being deleted. If the unique ID is an object, docid is an object as well. Fields of docid are read-only
* Read
  * $document: The document being read

* Tables: All tables defined in metadata are available under $tables object. For instance, '$tables.mytable.col1' points to the current contents of 'mytable.col1'. The table and column names are not case sensitive.

Variables are untyped. They get the type of the object they're assigned to. A new variable is created the first time a unique variable name is written.

## Column/Variable References

### Variable reference
```
varName
```
Value of the expression is the value of the variable. Can be used as a L-value or R-value.

### Column reference
```
{ column: columnName }
{ column: columnName, var: varName }
```
Value of the expression is the value of the column. When used as an L-value, sets the var value as well. When used as a R-value, column value is first set to variable value.

```
{ column: columnName, value: v }
```
This can only be used as an R-value, and sets column value to 'v'.

## Variables

### Set variable value
```
{ "$set": "varName", "var": "varName" }
{ "$set": "varName", "value": value }
{ "$set": "varName", "value": [] }
{ "$set": "varName", "valueof" : script }
```
Sets the value of 'varName' to the given value, the value of the variable, or the result of the script. The 'value:[]' initializes the variable to an empty list.

 * $set operation can be used to assign values to the columns of a table, or to a document
```
{ "$set": "$tables.MY_TABLE", "var":"$document" }
```
This operation sets the columns of MY_TABLE to the current values in $document based on the column mapping. Only those columns of 'MY_TABLE' that have a mapping in '$document' are initialized.

```
{ "$set": "$document.obj", "var":"$tables.SOME_TABLE" }
```
This operation sets the contents of 'obj' to the column values of 'SOME_TABLE'. Only the fields in 'obj' that have a mapping for a column of 'SOME_TABLE' are initialized.

```
{ "$reset":"$document.obj" }
```
This operation sets all values of 'obj' to null.


## SQL clauses
SQL clauses are the building blocks of SQL statements.
```
clause := { "clause": clause, "bindings":[ binding, ... ] }
```
The 'clause' is a string optionally containing value markers '?'. Each value in the 'bindings' will be assigned to the matching '?'. Each binding is a variable, or value.

```
binding := var |
           { var:variable "out": true } |
           { value: value }
```
The optional 'out' attribute determines if the binding is an OUT binding. OUT bindings read value from the executed statement and sets the variable. By default all bindings are IN bindings. A value binding cannot be an OUT binding.

## Variable/Column lists
SQL statements get a list of variables/columns to operate on. These lists can contain variable and/or column references:
```
 columns: [ column-reference, variable-reference,... ]
```
A column reference directly identifies a column. For variable references, the column mapping of the document field is used. A non-document variable cannot be used for column lists.

The following keywords can also be used:

* $document.$non-null-fields is an array [ {var: varName } ] containing all non-null fields for the current table
* $document.$all-fields is an array [ {var:varName} ] containing all the fields for the current table
* $document.$all-modified-fields is an array [ {var:varName} ] containing all the modified fields for the current table

## Operations

### foreach

```
{ foreach : { field: <array field name>,
              elem: <iteration temp variable name>,
              do:  <operation to perform> } }
```

This should be used to iterate through the elements of an embedded array.

  - field: The array field whose fields will be iterated
  - elem : This corresponds to the counter variable. At each iteration, elem points to the next array element.
  - do : Operations to perform for each iteration

For instance:
```
{ foreach : { field: arr, elem:x, do : { insert_row : { table : mytable } } } }
```

This will insert a row to 'mytable' for every element of arr. At each iteration, the variable 'x' 
will contain an element of 'arr', and all non-null fields of 'x' will be inserted.

### conditionals

```
{ "conditional" : { "test": { testScript }, "then":[...], "else":[...] } }
```
For instance:
```
{ "conditional": { "test": { "empty":"var" }, "then": thenScript, "else": elseScript } }
```
If 'var' is empty, runs 'then' script, otherwise 'else' script. 'var' can be a resultset or an array field.




### SQL

```
{ execute_sql: { clause: query, bindings: [ bindings...], resultBindings: [ bindings... ] } }
```

Executes a SQL statement. `bindings` are IN or OUT parameters to the
SQL statement. `resultBindings` are bindings to the columns of the
result set of the operation, if it has a result set and that result
set has at most one row. If the result has has more rows, an exception
is thrown. Order of bindings and resultbindings are important,
bindings order has to match the markers '?' and resultbindings order
has to match the columns.


### insert_row

```
{ insert_row : { table : <tableName> }
}
```

This procedure inserts a row to a table using the current values of the columns for $tables.tableName.

### update_row

```
{ update_row : { table: <tableName>,
                 row : [ ... ],
                 where : { where clause } 
          }
}
```
  - table: The table name to update a row
  - row Columns to be updated
  - where: Defaults to a WHERE clause written by the identifiers of the entity for tableName. 
    Can specify a WHERE clause (without the "WHERE") with bindings:
```
       ..., where: { clause:"id=? and active=?", bindings: [ $document.id, {value:true}] } 
```


### delete_row

```
{ delete_row : { table: <tableName>,
                 where: { clause:"criteria", bindings: [...] } 
               } 
}
```

### select

```
{ select : { project: [ var1, var2, {"clause": clause, bindings: [ ... ] },  ... ],
             distinct: true|false,
             join: { tables: [ { "alias": alias, "table": table, outer: false },...], on: { clause: "criteria", bindings:[...] } } |
             table: "table",
             where: { clause: "criteria", bindings: [...] },
             sort: [ { column: col, ascending: true}, { var: field, ascending: true} .,,, ] }
```

Builds a select statement. The columns must refer to the tables in the
join statement. If the tables in the join statement are aliased, the
projection columns must also use the aliased names. 

### collection_update

{ collection_update : { collection: <collectionField>,
                        retrieval: <sql script that retrieves the collection>,
                        inserted_rows: <Script that will be called for each inserted row, or variable>,
                        updated_rows: <Script that will be called for each updated row, or variable>,
                        deleted_rows: <Script that will be called for each deleted row, or variable>,
                        } 
}
```

This does the following:
  - Using 'retrieval' criteria,  retrieves a collection of rows 
  - Computes a list of inserted rows, updated rows, and deleted rows by comparing the 
    loaded collection and 'collectionField'
  - inserts/updates/deletes rows using the scripts, or assigns those lists to the variables
    - If a script is used, script can access the current row using '$currentrow'


