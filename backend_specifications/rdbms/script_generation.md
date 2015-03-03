# SQL Script Generation

The default operation mode of the RDBMS module is to generate SQL
scripts based on the field/column mappings and foreign key information
declated in the metadata.

## Query Generation

Query  generation takes a  projection, query,  and sort  criteria, and
builds a SQL  script using field mappings and  foreign key constraints
defined in metadata.

### Projection generation
 - Build a set of fields to retrieve using the projection, query, and sort criteria in the request
 - Build a projection clause:
```
   project: { {field:field1}, {field:field2}, ... }
```
 - The projection clause uses the readFilters for each field

### Join generation

 - Construct a graph where each table is a node and foreign keys are edges. 
 - Determine the minimal set of fields required to be retrieved based on projection, query, and sort
 - Determine the minimal set of tables required to join to retrieve those fields.
 - Find a minimal tree in the table graph that includes all the required tables
   - If more tables are required, add those tables
 - Generate a join clause:
```
   join: { tables: [t1, t2,...], on:{ ... } }
```
 - Aliasing: to assign the alias X to a table T:
```
  { "X":"T" }
```
   
 - Generate join criteria:
   - If t1 and t2 are included in join and t1 has a FK referencing to t2:
```
    on: { sql: "(t1.fkcol_1=t2.pkcol_1 and t1.fkcol_2=t2.pkcol_2 and ...)" }
```

#### Join customization

Joins can be customized in metadata.

```
"rdbms" : {
  "tables" :[ ... ],
  "joins" : [
      {
        "tables": [ t1, t2, t3... ],
        "on" : { sql:criteria, bindings:[...] }
      },
      ...
   ]
}
```

If the set of tables in a join matches the tables of a join definition
in metadata, the given join criteria is used instead of generating
criteria.


### WHERE clause generation

For a given search criteria X, the where clause whereClause(X) is generated recursively as follows:

 - { $not : X } =>  NOT ( whereClause(X) )
 - { $and: [ X_1, X_2, ... ] } => ( whereClause(X_1) AND whereClause(X_2) AND ... ) (same for $or)
 - { field: f, op=OP, value:x } => readFilter(f) OP x
 - { field: f, op=OP, rfield: g } => readFilter(f) OP readFilter(g)
 - { field: f, op=OP, values: [ ...] } => readFilter(f) OP [...] (OP=in or not in)
 - { field: f, op=OP, rfield: arrayField }
    - If arrayField is in a different table, then:
         readFilter(f) OP { $select : { join: [ arrayFieldTable ], project: [ col ], where: {...} } }
 - { field:f, regex: pattern } => readFilter(f) LIKE pattern 
 - { array:f, contains: OP, values: [...] }
    - If f is in a different table, then:
       - OP=$any:
         { $select: { join: [ arrayFieldTable ], project: [ col], where: [... AND (col in values)] } }
       - OP=$all:
          ?????????
       - OP=$none:
          ????????
 - { array:f, elemMatch: X } => whereClause(X) in context of f. This works because querying array 'f' adds that entity to the join list.


### Sort generation

For a given sort criteria S, the sortClause(S) is:
 - [ S_1, S_2, ... ] => ORDER BY sortClause(S_1), sortClause(S_2),...
 - { field:f, ascending:true} => columnOf(f) asc



## Insertion Generation

A recursive algorithm can be used to generate an insertion script:

 - Start from the table for the top level entity
 - If at any point there are any foreign keys referencing to another table, insert the referenced table first.
   - In order to insert a row for a table, all the foreign keys must have referencing rows already inserted
   - While following foreign keys, if the destination table maps to an array, insert using a 'foreach' clause. Otherwise insert only one row.

### ID generation
Override the table insert default procedure.

Example:
```
  insert: [    { sql : { sql: "select seq.next from dual", resultbinding: [ _id ] } },
               { insert_row : { table: BASE } } ]
```

