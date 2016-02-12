# fields
Defines the model for the entity.  Structure is based on json schema v4-draft.

There are three types of field definitions:
* `simple`: basic data types such as string and date.
* `container`: object or array structures
* `reference`: references to other entities

All field definitions in the `fields` structure share common attributes:

```javascript
fields: {
    "fieldName": {
        "access": { ... },
        "constraints": { ... },
        "description": "Field description.",
        "type": "fieldType"
    }
}
```

* `fieldName`: The name of the field as the property name in the JSON document. [required]
* `access`: Roles required for accessing a field. [optional]
* `constraints`: Constraints on the field. [optional]
* `description`: Description of the field. [optional]
* `type`: the type of field. One of: [required]
    * boolean, integer (64-bit), string, double, biginteger, bigdecimal, date, binary
    * array, array
    * reference


## Examples

TODO
* enum
* complex identity
* rray identty
* object
* array
* array with object
* array with object with array with object (

