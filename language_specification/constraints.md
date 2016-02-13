# constraints

Constraints influence how a field behaves.  In this section each possible constraint is documented including what field `type` values the constraint applies to.

The `constraints` field on a field is an object with key value pairs.  The key is the constraint name, the value is the value for that constraint.

```javascript
"constraints": {
    "constraint 1": "value",
    "constraint 2": false,
    ...
}
```

## identity
Fields with the `identity` constraint set to `true` are used to identify documents.  If more than one field is marked with `identity` they are all used, meaning it's a composite key.

```javascript
"constraints": {
    "identity": true,
    ...
}
```

Applies to any field.

## required
Fields with `required` constraint set to `true` are requried to have a non-null value for insert and update operations.  By default fields are not required and can be null.

```javascript
"constraints": {
    "required": true,
    ...
}
```

Applies to any field.

## enum
The field references an [enumeration defined in entityInfo](enums.md).

```javascript
"constraints": {
    "enum": "enumName",
    ...
}
```

Applies to fields with `type` of: `string`

## matches
On insert and update of a field with the `matches` constraint, the field value is checked against the given regular expression.

```javascript
"constraints": {
    "matches": "regular expression",
    ...
}
```

Applies to fields with `type` of: `string`

## minLength & maxLength
Constraints to restrict the minimum or maximum length of a string field.

```javascript
"constraints": {
    "minLength" : 10,
    "maxLength" : 256,
    ...
}
```
Applies to fields with `type` of: `string`

## element-identity
Provides identity for an element on an array.  The value for fields with the `element-identity` constraint must be unique on that array across all instances of the entity.  The difference with `identity` is this constraint does not define *document* identity.

```javascript
"constraints": {
    "element-identity": true,
    ...
}
```

Applies to any field with a simple `type` that is a child of an `array`.

## minItems & maxItems
Constraint to restrict the minimum or maximum number of elements in an array.  Default is 0 minimum and unlimited maximum.

```javascript
"constraints": {
    "minItems": 1,
    "maxItems": 12,
    ...
}
```
Applies to fields with `type` of: `array`

## minimum & maximum
Constrain minimum or maximum value for numeric types.

```javascript
"constraints": {
    "minimum": 10,
    "maximum": 4589,
    ...
}
```

Applies to fields with `type` of: `bigdecimal`, `biginteger`, `double`, `integer`
