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
