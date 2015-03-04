# SQL Script Language

A script contains one or more statements:
```
script := statement | [ statement, statement, ... ]
```

A statement has a name, and arguments:
```
statement := { name : { args } }
```

Arguments may contain scripts.

Each statement represents an algorithm that can be invoked to perform
an operation. This algorithm can be the execution of a SQL statement,
or execution of a particular logic that involves multiple SQL
statements. 


## Column/Field References

For statements requiring values, columns, fields, temporary variables, or values can be used.

### Field reference
```
{ field: fieldName }
```
When read, reads the field value. When written, writes the field value. 
The field table/column mappings are used to determine which column is used.

```
{ field: fieldName, value: value }
```
Invalid for reads. When written, sets the field value to 'value'.


### Column reference
```
{ column: columnName, field: fieldName }
```
When read, reads the column and sets the fieldName to that value. 
When written, reads fieldName and sets the column value.

```
{ column: columnName, value: value }
```
Invalid for reads. When written, sets the column to value.

```
{ column: columnName, variable: varName }
```
When read, read the colum and sets the temporary variable 'varName'.
When written, sets the column value from the temporary variable 'varName'.

For all the above cases, optionally a sql clause can be specified:

```
{ field: fieldName, sql:sqlClause }
```
Uses the sqlClause to read/write the column. The sql clause must have one '?'. Note that for all above cases where sql is not specified, sql defaults to '?'.

```
{ sql: sqlClause, bindings: [ binding,... ] }
```
Uses sqlClause to read/write a column or columns, and associates that value(s) with the bindings. Bindings can be:

  - { field: fieldName }
  - { variable: variableName }
  - { value: value }

* $non-null-fields is an array [ {field: fieldName } ] containing all non-null fields for the current table
* $all-fields is an array [ {field:fieldName} ] containing all the fields for the current table
* $all-modified-fields is an array [ {field:fieldName} ] containing all the modified fields for the current table

* For INSERT operations, field references refer to the document being inserted.
* For UPDATE operations, two pseudo-fields are defined: '$new' and '$old'. '$new' refers to the document containing the values that will be written. '$old' refers to the values that are in the database before the update. '$new.x' refers to the new value of field 'x', and '$old.x' refers to the value of 'x' in the db before the update operation. Unqualified references assume '$new', i.e. 'x' will refer to '$new.x'.
* DELETE operations only assume that the unique identifiers for the entity are known. If more information is necessary to delete any associated data in other tables, the DELETE script should read them.

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
       ..., where: { sql:"id=? and active=?", bindings: [ {field: id}, {value:true}] } 
```


### delete_row

```
{ delete_row : { table: <tableName>,
                 where: { sql:"criteria", bindings: [...] } 
               } 
}
```

### select

```
{ select : { project: [ col1, col2, {field: field3 },  ... ],
             distinct: true|false,
             join: { tables: [ table1, table2,...], on: { sql: "criteria", bindings:[...] } },
             where: { sql: "criteria", bindings: [...] },
             sort: [ { column: col, ascending: true}, { field: field, ascending: true} .,,, ] } }
```

Builds a select statement. The columns must refer to the tables in the
join statement. If the tables in the join statement are aliased, the
projection columns must also use the aliased names.

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


### resultset
Assigns the resultset of a query to a variable.
```
{ resultset: { name: variableName, value: query } }
```
Runs the `query`, and assigns the result set of the query to `variableName`. That variable name then can be used in `if` or `foreach` statements.

### conditionals

```
{ ifempty: var, then: { ... }, else: {...} }
```
If 'var' is empty, runs 'then' script, otherwise 'else' script. 'var' can be a resultset or an array field.


