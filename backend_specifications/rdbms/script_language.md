# SQL Script Language

A script contains one or more statements:
```
script := statement | [ statement, statement, ... ]
```

A statement has an operation name, and arguments:
```
statement := { operationName : { args } }
```

Each statement represents an algorithm that can be invoked to perform
an operation. This algorithm can be the execution of a SQL statement,
or execution of a particular logic.

## Variables
For each different operation, scripts execute with a set of variables accessible to them. These
are the variables that are predefined for each operation.

* Insert
  * document: The document being inserted.
* Update
  * document: The new copy of the document. 
  * olddocument: The unmodified copy of the document. Fields of olddocument are read-only.
* Delete
  * docid : The unique ID pf the document that's being deleted. If the unique ID is an object, docid is an object as well. Fields of docid are read-only
* Read
  * document: The document being read

Variables are untyped. They get the type of the object they're assigned to. A new variable is created the first time a unique variable name is written.

## Column/Variable References

For statements requiring values, columns, variables, or values can be used.

|Expression|L-value|R-value|
|-|-|-|
|var: varName|+|+|
|column: columnName|+|+|
|value: v|-|+|


### Variable reference
```
{ var: varName }
```
Value of the expression is the value of the variable. Can be used as a L-value or R-value.

### Column reference
```
{ column: columnName }
{ column: columnName, var: varName }
```
Value of the expression is the value of the column. When used as a L-value, sets the var value as well. When used as a R-value, column value is first set to variable value.

```
{ column: columnName, value: v }
```
This can only be used as an R-value, and sets column value to 'v'.

## SQL clauses
SQL clauses are the building blocks of SQL statements.
```
clause := { "clause": clause, "bindings":[ binding, ... ] }
```
The 'clause' is a string optionally containing value markers '?'. Each value in the 'bindings' will be assigned to the matching '?'. Each binding is a variable, or value.

```
binding := { var:variable "out": true }
binding := { value: value }
```
The optional 'out' attribute determines if the binding is an OUT binding. OUT bindings read value from the executed statement and sets the variable. By default all bindings are IN bindings. A value binding cannot be an OUT binding.

## Variable/Column lists
SQL statements get a list of variables/columns to operate on. These lists can contain variable and/or column references:
```
 columns: [ column-reference, variable-reference,... ]
```
A column reference directly identifies a column. For variable references, the column mapping of the document field is used. A non-document variable cannot be used for column lists.

The following keywords can also be used:

* $non-null-fields is an array [ {var: varName } ] containing all non-null fields for the current table
* $all-fields is an array [ {var:varName} ] containing all the fields for the current table
* $all-modified-fields is an array [ {var:varName} ] containing all the modified fields for the current table

## Statements

### insert_row

```
{ insert_row : { table : <tableName>,
                 columns: [ ... ]  }
}
```

This procedure inserts a row to a table using the current document.
  - table: The table name to insert a row
  - columns: Columns to be inserted. 

### update_row

```
{ update_row : { table: <tableName>,
                 columns : [ ... ],
                 where : { where clause } 
          }
}
```
  - table: The table name to update a row
  - columns: Columns to be updated
  - where: Defaults to a WHERE clause written by the identifiers of the entity for tableName. 
    Can specify a WHERE clause (without the "WHERE") with bindings:
```
       ..., where: { clause:"id=? and active=?", bindings: [ {var: document.id}, {value:true}] } 
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
{ select : { project: [ col1, col2, {var: document.field },  ... ],
             distinct: true|false,
             join: { tables: [ { "alias": alias, "table": table, outerJoin: false },...], on: { clause: "criteria", bindings:[...] } },
             where: { clause: "criteria", bindings: [...] },
             sort: [ { column: col, ascending: true}, { var: field, ascending: true} .,,, ],
             resultSet: varName } }
```

Builds a select statement. The columns must refer to the tables in the
join statement. If the tables in the join statement are aliased, the
projection columns must also use the aliased names. If 'resultSet' is specified, a new
variable 'varName' is assigned to the resultset of the select statement.

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



### collection_update

{ collection_update : { field: <collectionField>,
                        table: <tableName>,
                        retrieval: <sql script that retrieves the collection>,
                        inserted_rows: <Script that will be called for each inserted row>,
                        updated_rows: <Script that will be called for each updated row>,
                        deleted_rows: <Script that will be called for each deleted row>
                        } 
}
```

This does the following:
  - Using 'retrieval' criteria,  retrieves a collection of rows from table 'tableName'
  - Computes a list of inserted rows, updated rows, and deleted rows by comparing the 
    loaded collection and 'collectionField'
  - inserts/updates/deletes rows using the scripts


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
{ sql: { clause: query, bindings: [ bindings...], resultBindings: [ bindings... ], resultSet: varName } }
```

Executes a SQL statement. `bindings` are IN or OUT parameters to the
SQL statement. `resultBindings` are bindings to the columns of the
result set of the operation, if it has a result set. Order of bindings
and resultbindings are important, bindings order has to match the
markers '?' and resultbindings order has to match the columns. 'resultSet' is the
name of a variable that is assigned the resultset, if any.
