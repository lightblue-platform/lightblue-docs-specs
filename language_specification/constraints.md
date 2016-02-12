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

```javascript
"constraints": {
    "identity": true,
    ...
}
```

Applies to any field.

## required
   *  required: boolean indicating if the field is required, default is false.

```javascript
"constraints": {
    "required": true,
    ...
}
```

Applies to any field.

## enum
   *  enum reference where the enum name comes from entityInfo

```javascript
"constraints": {
    "enum": "enumName",
    ...
}
```

Applies to fields with `type` of: `string`

## matches

```javascript
"constraints": {
    "matches": "regular expression",
    ...
}
```

Applies to fields with `type` of: `string`

## minLength & maxLength

*  constraints: array of field constraint objects
   *  minLength, maxLength for strings (minLength=1 is to be used to mean nonempty string)

```javascript
"constraints": {
    "minLength" : 10,
    "maxLength" : 256,
    ...
}
```
Applies to fields with `type` of: `string`

## element-identity

```javascript
"constraints": {
    "element-identity": true,
    ...
}
```

Applies to any field with a simple `type` that is a child of an `array`.

## minItems & maxItems
   *  minItems and maxItems limit number of items in an array.  Default is 0 to unlimited.

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
