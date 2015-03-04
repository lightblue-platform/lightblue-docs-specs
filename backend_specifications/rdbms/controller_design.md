# Controller Design
New controllers are an interesting challenge for lightblue.  You're taking some existing backend datastore and mapping it's capabilities into a general framework (lightblue) that represents all data as JSON documents.  For many backend systems this is not a particular challenge.  For example, MongoDB stores BSON documents and therefore is almost 1:1 with the JSON documents that lightblue presents to clients.  RDBMS is different in two ways.  First, relational databases do not directly support the document structure needed by lightblue.  Second, there's an assumption that the RDBMS controller will be used against existing schemas.  This assumption of existing schemas also means the schemas cannot change.  If they already exist, have data, and are important it means something is already reading from and writing to the schema.  This creates an additional assumption:  the existing applications are not going to change and therefore the existing RDBMS schema is immutable.  Transformation is therefore needed between the backend RDBMS structure and the JSON document presented to the client.

## Assumptions
For the RDBMS controller:
* RDBMS does not support document structures
RDBMS schemas will exist already
* RDBMS schemas are immutable
* Each RDBMS vendor is different.  While there may be some overlap it is assumed that there is going to be vendor specific processing.

## Definitions
There are few things we need to define up front.  Each will be discussed separately in this document.
* data types - what are the data types for RDBMS and lightblue?
    * Note that in each data type section the data types of higher priority (known to be used internally and would be used in lightblue metadata in the next 1-2 years) are marked in bold.
* data type mappings - what data types can be mapped between RDBMS and lightblue?
* other mappings - what other things need mapped (tables, documents, arrays, etc)?

## Scenarios
There are many permitations to consider when designing a CRUD controller.  This design document should be fairly exhaustive.

### Recommendations
The nuances of simple field mapping scenarios should be tested independently.  Do not mix scenarios as it makes understanding and maintaining things more difficult.  For example, if you need to test mapping RDBMS VARCHAR2 to lightblue date, test just that in a simple document.  Do not add in complexities such as sub-documents or arrays.  Similarly, when testing something like mapping a flat RDBMS table to a lightblue entity with sub-documents, do not add complex field mappings into the mix.  Keep field mapping examples simple such as RDBMS VARCHAR2 to lightblue string.

## Design Notes

### DEFAULTING TABLE / COLUMN NAMES
It is a pain for simple metadata to have to specify table and column information.  Propose:
in the entityInfo datastore we can specify a default table
if column for a field is missing it is assumed to be the same as the field name

### DIALECT
Configuration must indicate the dialect.  Maybe it could be pulled from the datasource configuration.  I'm assuming not and putting it in metadata for now.

### OVERRIDE DEFAULT FOR EACH OPERATION PER FIELD
For field mappings, it might be useful to have a mechanism to define further customization depending on operation. The most obvious way is to define how the field is used in insert/update/select statements. Something like this:

```json
{
    "fieldName": {
        "type": "string",
        "rdbms": {
            "table": "tableName",
            "column": "columnName",
            "readFilter": "'?'",
            "writeFilter": "'?'"
        }
    }
}
```

### SQL STATEMENTS
You have to build the set clause field by field.  The metadata maintainer is not going to define each possible update a client could request.  Similar for insert unless all fields are required, how can they know what will be passed in?  There's risk with binding a NULL by default because underlying tables could have default columns.  We'll have to be clear about what is done for an insert in the case when a default value exists for an optional column that was not set on insert.

### BIND PARAMETERS
Probably necessary for PL/SQL.

### STANDARD FIELD TRANSFORMATIONS
Things like date need to have a standard transformation defined as part of the dialect.

### IDENTITY
Should identity be required?  Save can't work without some field to identify the record.  Is this already a constraint enforced in core?

For arrays there needs to be some way to identify each element in the array.  Scenarios are assuming that all fields with an identity constraint in an array's object are used for such identitification.


### GENERATED OPERATIONS AND SQL SCRIPTS
We will generate the SQL operations using key relationships defined in metadata. However, it is not always possible to generate SQL operations because of some implicit logic that cannot be defined in metadata. Remember: modifying the underlying schema is not an option. For these cases, we should allow metadata definer to describe the logic to implement a particular operation. To support this, the controller is structured into two logical layers:
  * SQL scripts encoded using a grammar represented in JSON. Using this language, metadata maintainer can define the operations required to insert/update/delete entities. If metadata declarations are not sufficient to implement the underlying logic, the metadata maintainer should use this procedural form to describe operations.
  * Generation of SQL scripts using key relationships in metadata. This is the default mode of use. This case should be able to handle most property normalized cases.

In normal operation (i.e. no SQL scripts are defined), the controller should generate SQL scripts using metadata, and execute those scripts. If SQL scripts are defined in metadata, the controller should simply execute those declared scripts.
