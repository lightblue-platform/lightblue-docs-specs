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
  * $documentId : The unique ID of the document that's being deleted. If the unique ID is an object, docid is an object as well. Fields of docid are read-only (What if it is a composite ID?)
* Read
  * $document: The document being read
* Tables
  * All tables defined in metadata are available under $tables object. For instance, '$tables.mytable.col1' points to the current contents of 'mytable.col1'. The table and column names are not case sensitive.

Variables are untyped. They get the type of the object they're assigned to. A new variable is created the first time a unique variable name is written.

Variable assignments work by value, not reference.

### Value types

These are the value types that can be used in scripts:
* Primitive types: Any java type, including date, String, etc. Accessing simple document 
fields, column values yield values of these types.
* LOBs: Arrays of bytes or characters.
* Lists: Result sets, or Json arrays 
* Maps: Json object nodes

## Column/Variable References

### Variable reference
```
varName
```
Value of the expression is the value of the variable. Can be used as a L-value or R-value.

An L-value is an expression to which a value can be assigned (can appear on the LEFT side of an assignment operator).

An R-value is an expression whose value can be read.

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
{
    "$set": {
        "dest": "varName1",
        "var": "varName2" 
    }
}
{
    "$set": {
        "dest": "varName1",
        "value": value }
    }
}
{
    "$set": {
        "dest": "varName1",
        "value": []
    }
}
{
    "$set": {
        "dest": "varName1",
        "valueOf": <script>
    }
}
```
Sets the value of 'dest' to the given value, the value of the variable, or the result of the script. The 'value:[]' initializes the variable to an empty list.

 * $map operation can be used to map values of the columns of a table to a document, or vice versa
```
{ "$map": { "dest":"$tables.MY_TABLE", "source":"$document" } }
```
This operation sets the columns of MY_TABLE to the current values in $document based on the column mapping. Only those columns of 'MY_TABLE' that have a mapping in '$document' are initialized.

```
{ "$map": { "dest": "$document.obj", "source":"$tables.SOME_TABLE" } }
```
This operation sets the contents of 'obj' to the column values of 'SOME_TABLE'. Only the fields in 'obj' that have a mapping for a column of 'SOME_TABLE' are initialized.

```
{ "$null": { "var":"$document.obj" }}
```
This operation sets all values of 'obj' to null.


## SQL clauses
SQL clauses are the building blocks of SQL statements.
```
sql-clause := { "clause": clause, "bindings":[ binding, ... ] }
```
The 'clause' is a string optionally containing value markers '?'. Each value in the 'bindings' will be assigned to the matching '?'. Each binding is a variable, or value.

```
binding := var |
           { var:variable, "dir":"in"|"out"|"inout", "type":type } |
           { value: value. "type":type }
```
OUT bindings read value from the executed statement and sets the variable. By default all bindings are IN bindings.  A value binding cannot be an OUT binding. A 'type' is required for all OUT and INOUT bindings. Possible values are: http://docs.oracle.com/javase/7/docs/api/java/sql/Types.html

### Examples
IN binding:
```
{"clause": "?", "bindings:["$someVariable"]}
{"clause": "?", "bindings:[{"var":"$someVariable","dir":"in"}]}
{"clause": "?", "bindings:[{"value":1}]}
```

OUT binding:
```
{"clause": "?", "bindings:[{"var":"$someVariable","dir":"out"}]}
```

IN/OUT binding:
```
{"clause": "?", "bindings:[{"var":"$someVariable","dir":"inout"}]}
```

VALUE binding:
```
{"clause": "?", "bindings":[{"value":12345}]}
```


## Operations

### foreach

```
{ foreach : { var: <array field name>,
              elem: <iteration temp variable name>,
              do:  <operation to perform> } }
```

This should be used to iterate through the elements of an embedded array.

  - var: The array field whose fields will be iterated
  - elem : This corresponds to the counter variable. At each iteration, elem points to the next array element.
  - do : Operations to perform for each iteration

For instance:
```
{ foreach : { var: "$document.arr", elem:x, do : { insert_row : { table : "$tables.mytable" } } } }
```

This will insert a row to 'mytable' for every element of arr. At each iteration, the variable 'x'
will contain an element of 'arr', and all non-null fields of 'x' will be inserted.

### conditionals

```
{ "conditional" : { "test": { testScript }, "then":[...], "else":[...] } }
```
For instance:
```
{ "conditional": { "test": { "isEmpty":"var" }, "then": thenScript, "else": elseScript } }
```
If 'var' is empty, runs 'then' script, otherwise 'else' script. 'var' can be a resultset or an array field.


#### Tests

```
 { "isEmpty" : var }
 { "isEmpty" : { "var": variable } }
 { "isEmpty" : { "value" : value } }
```


### SQL

```
{ sql: { clause: query, bindings: [ bindings...] } }
{ sqlcall: { clause: query, bindings: [ bindings,...] } }
```

Executes a SQL statement. 'sql' is used for all queries and update
statements. sqlcall is used for calling stored procedures, or inline
functions. `bindings` are IN or OUT parameters to the SQL
statement. No OUT parameters are allowed for 'sql. If the statement is
an update, insert, or delete statement, the return value is an integer
with number of rows modified. If the statement is a query, the return
value is a result set.

For queries involving many results, assign the operation to a
variable, and use 'apply_binding' within a for-each  to retrieve results.

sqlcall returns null. sql may return a result set, or updated row
count, depending on the type of clause.

### apply_binding

```
{ apply_binding: { row: list, bindings: [bindings,...] } }
```

'row' is a list containing a row of data, where each column of the row
is an element of the list. apply_binding copies each column of 'row' to the
corresponding 'binding'. The variables in bindings are all assumed to be OUT variables.


### insert_row

```
{ insert_row : { table : "$tables.<tableName>" } }
```

This procedure inserts a row to a table using the current values of the columns for $tables.tableName.

### update_row

```
{ update_row : { table: "$tables.<tableName>",
                 columns : [ "col1","col2",... ],
                 where : { sql-clause }
          }
}
```
* table: The table name to update a row
* columns: Columns to be updated. If omitted, all columns are updated
* where: Defaults to a WHERE clause written by the identifiers of the entity for tableName.

Can specify a WHERE clause (without the "WHERE") with bindings:
```
where: { clause:"id=? and active=?", bindings: [ $document.id, {value:true}] }
```

### delete_row

```
{ delete_row : { table: <tableName>,
                 where: { sql-clause }
               }
}
```

### update_collection 

{ update_collection : { collection: <collectionField>,
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


