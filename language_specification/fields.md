# fields
Defines the model for the entity.  Structure is based on json schema v4-draft.

There are three types of field definitions:
* `simple`: basic data types such as string and date.
* `object`: complex object structure
* `array`: an array of either simple or object fields.

All field definitions in the `fields` structure share common attributes:

```javascript
fields: {
    "fieldName": {
        "access": { ... },
        "constraints": { ... },
        "description": "Field description."
        "type": "fieldType"
    }

}
```

* `access`: Roles required for accessing a field. [optional]
* `constraints`: Constraints on the field. [optional]
* `description`: Description of the field. [optional]
* `type`: the type of field. One of: [required]
    * boolean, integer, string, double, biginteger, bigdecimal, date, binary
    * object
    * array


### fields_object

* "name": name of the field, this is the property name in the json document
* type: one of:
   *  basic types: boolean, integer (64-bit int), string, double, biginteger, bigdecimal, date, binary, uid
       * If a field is defined as biginteger, or bigdecimal in metadata, store the value as string, and don't allow &lt; &gt; operators during search.operators are not supported for this field during search.
       * binary - an open-ended data type for storing binary data, it is recommended that each use of this field clearly indicates what encoding is used.  For example, in java using DatatypeConverter.printBase64Binary(byte[]) the encoding spec is http://www.w3.org/TR/xmlschema-2/#base64Binary.  Note there are limits on document sizes, see [MongoDB Limits and Thresholds](http://docs.mongodb.org/manual/reference/limits/#BSON-Document-Size) documentation.
       * uid - unique identifier field that can be used anywhere within a document, specifically created to support identity of array elements. If an uid field is not initialized or null, then upon inserting/updating the document, the uid field is initialized to a unique string value. If a uid field has value during insert/update, it is not modified.
   * date: Re: How will we represent date in JSON?
   * a container type: object, array
* fields: If type=object, an array of field objects
* items: If type=array, an object of the form:
```javascript
    "items" : {
      "type": type
      "fields" : { ... } (if type=object)
    }
```
* reference: If type=object or array. If reference is present, fields and elements cannot be given. Determines a query, projection, and sort order for another entity to be inserted into the document
```javascript
    "reference" : {
      "entity": entityName,
      "versionValue": versionValue,
      "projection": projection_expression,
      "query": query_expression,
      "sort": sort
    }
```
* access: field access object. Determines the access rights of the field
```javascript
    "access" : {
       "find" : [ role1, role2, ... ],
       "insert": [ role1, role2, ...],
       "update": [ role1, role2, ... ]
    }
```

  *  find: If the caller does not have the required role, the field is removed from the return value
  *  update: If the caller does not have the required role, the field will not be updated. Attempt to update inaccessible fields will result in failed update
  *  insert: If the caller does not have the required role, attempt to set field value during insertion will result in failed insert.
*  constraints: array of field constraint objects
   *  minLength, maxLength for strings (minLength=1 is to be used to mean nonempty string)
```javascript
    { "minLength" : value }
    { "maxLength" : value }
```
   *  minItems and maxItems limit number of items in an array.  Default is 0 to unlimited.
```javascript
    { "minItems": value }
    { "maxItems": value }
```
   *  required: boolean indicating if the field is required, default is false.
```javascript
    { "required": true|false }
```
   * minimum/maximum (for number types)
```javascript
    { "minimum": value }
    { "maximum": value }
```
   *  enum reference where the enum name comes from entityInfo
```javascript
    { "enum": enum name }
```
   * references: denotes fields in other entities this field depends on. The constraint requires that if this field value is non-null, there must be an instance of entity with { entityField: thisField }
```javascript
    {
        "references": {
           "entityName": entityName,
           "versionValue": version.value
           "entityField": fieldName
        }
    }
```


*  hooks: optional array of enums that define what hooks are triggered when CRUD operations are performed on this field. Hooks are invoked in the given sequence.
   *  Values can be "audit" or "publish" at this time.  See How will events for create, update, and delete be sent to the ESB?
```javascript
    {
        "hooks": {
            "insert":[value1...],
            "update": [value1...],
            "delete": [value1...]
        }
    }
```

### Predefined Fields

The following fields are stored with every entity. If the metadata
defines these as fields, they are exposed to the callers. Otherwise,
these fields will not be exposed.


*  &lt;arrayField&gt;#: arrayField is an array field of the entity. The variable &lt;arrayField&gt;# keeps the number of elements in the array
  * Type: int
  * read-only

* object_type: Entity type
  * Type: string
  * read-only
