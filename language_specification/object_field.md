# container field
There are three types of `type` values:
* simple
    * `bigdecimal`, `biginteger`, `boolean`, `binary`, `date`, `double`, `integer`, `string`
* container
    * `array`, `object`
* reference
    * `reference`

This section is focusing on the container types.

## object
Allowing for nested document structures, the `object` type follows the same definition as the [fields](fields.md) attribute at the root of the `schema`.

```javascript
"objectFieldName": {
    "type": "object",
    "fields": { ... }
}
```

* `fields`: The structure for the object.  See documentation on [fields](fields.md)

## array
You can have an array with different types of contents.  It can be a:
* simple array: contains a single simple type of data
* object array: array of objects of any structure
* multi-dimentional array: array of arrays

For any array field there are also [predefined fields](#predefined-fields) that are created.

```javascript
"arrayFieldName": {
    "type": "array",
    "items" : {
        "type": "the type",
        ...
    }
}
```

* `items`: object defining the structure / type for each element in the array

### simple array
```javascript
"arrayFieldName": {
    "type": "array",
    "items" : {
        "type": "simple type"
    }
}
```
Where `type` is one of the [simple types](field_type.md).

### object array
```javascript
"arrayFieldName": {
    "type": "array",
    "items" : {
        "type": "object",
        "fields": { ... }
    }
}
```

* `fields`: see [object](#object) section in this document

### multi-dimentional array
```javascript
"arrayFieldName": {
    "type": "array",
    "items" : {
        "type": "array",
        "items": { ... }
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
