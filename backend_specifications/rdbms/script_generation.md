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
