# SQL Script Generation

The default operation mode of the RDBMS module is to generate SQL
scripts based on the field/column mappings and foreign key information
declated in the metadata.

Note: We are not talking about script text generation. The output of the script generator is an executable script object tree.

## Query Generation

Query  generation takes a  projection, query,  and sort  criteria, and
builds a SQL  script using field mappings and  foreign key constraints
defined in metadata.

### Projection generation
 - Build a set of fields to retrieve using the projection, query, and sort criteria in the request
 - Build a projection clause:
```
   project: { var1, var2, ... }
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
All tables should be aliased.

 - Generate join criteria:
   - If t1 and t2 are included in join and t1 has a FK referencing to t2:
```
    on: { clause: "(t1.fkcol_1=t2.pkcol_1 and t1.fkcol_2=t2.pkcol_2 and ...)" }
```

#### Join customization

Joins can be customized in metadata.

```
"rdbms" : {
  "tables" :[ ... ],
  "joins" : [
      {
        "tables": [ t1, t2, t3... ],
        "on" : { clause:criteria, bindings:[...] }
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
 - { $and: [ X_1, X_2, ... ] } => ( whereClause(X_1) AND whereClause(X_2) AND ... )
 - { $or: [ X_1, X_2, ... ] } => ( whereClause(X_1) OR whereClause(X_2) OR ... )
 - { field: f, op=OP, value:x } => readFilter(f) OP x
 - { field: f, op=OP, rfield: g } => readFilter(f) OP readFilter(g)
 - { field: f, op=OP, values: [ ...] } => readFilter(f) OP [...] (OP=in or not in)
 - { field: f, op=OP, rfield: arrayField }
    - If arrayField is in a different table, then:
         readFilter(f) OP { $select : { join: [ arrayFieldTable ], project: [ col ], where: {...} } }
 - { field:f, regex: pattern } => readFilter(f) LIKE pattern 
    - QUESTION how are we dealing with translation of regex to LIKE statement?  They're not 1:1.
 - { array:f, contains: OP, values: [...] }
    - If f is in a different table, then (assuming x is the alias for the parent object's table):
       - OP=$any:
         { $select: { project: [ col ], join: { tables: [{alias:a, table: arrayFieldTable }], on: {clause:"a.fk1=x.pk1 AND ..."} }, where: {"criteria": "$document.f in (?,...)", bindings: [values]} } } }
       - OP=$all:
         { $select: { project: [ col ], join: { tables: [{alias:a, table: arrayFieldTable }], on: {clause:"a.fk1=x.pk1 AND ..."} }, where: {"criteria": "? IN $document.f AND ...", bindings: [values]} } } }
       - OP=$none:
         { $select: { project: [ col ], join: { tables: [{alias:a, table: arrayFieldTable }], on: {clause:"a.fk1=x.pk1 AND ..."} }, where: {"criteria": "? NOT IN $document.f AND ...", bindings: [values]} } } }
 - { array:f, elemMatch: X } => whereClause(X) in context of f. This works because querying array 'f' adds that entity to the join list.


### Sort generation

For a given sort criteria S, the sortClause(S) is:
 - [ S_1, S_2, ... ] => ORDER BY sortClause(S_1), sortClause(S_2),...
 - { field:f, ascending:true } => columnOf(f) asc
 - { field:f, ascending:false } => columnOf(f) desc
 - { field:f } => columnOf(f) desc

## Insertion Generation

A recursive algorithm can be used to generate an insertion script:

 - Start from the table for the top level entity
 - If at any point there are any foreign keys referencing to another table, insert the referenced table first.
   - In order to insert a row for a table, all the foreign keys must have referencing rows already inserted
   - While following foreign keys, if the destination table maps to an array, insert using a 'foreach' clause. Otherwise insert only one row.

QUESTION what if there is a cyclic FK relationship?  It's crazy but could happen.  Would assume at least one of the relationships was optional.  Assuming table A reference B, B references A, FK from A to B is optional, FK from B to A is required:  1) insert to A without FK to B, 2) insert to B with FK to A, 3) update A with FK to B

### ID generation
Override the table insert default procedure.

Example:
```
  { insert_row : [
       { execute_sql : { clause: "select seq.next from dual", resultBindings: [ $document._id ] } },
       { insert_row : { table: BASE } } ] }
```

## Update/Save generation

Save is implemented as an update of all fields, so only update generation will be handled.

Algorithm is the same for insertion, with the difference that instead
of inserting rows for associated entities, collection_update should be
used.
