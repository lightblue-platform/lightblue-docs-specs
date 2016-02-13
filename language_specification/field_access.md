# field access
Similar to the [entity access](access.md), field access allows you to optionally constrain access to specific fields (including objects and arrays) for whatever operation you wish to restrict.  By default access is set to `anyone`.

Note there is no `delete` access on a field.  Setting a field to `null` is an `update` operation.

```javascript
"access": {
    "find": [
        "find role 1",
        ...
    ],
    "insert": [
        "insert role 1",
        ...
    ],
    "update": [
        "update role 1",
        ...
    ]
}
```

* `find`: If the caller does not have the required role, the field is removed from the return value
  *  update: If the caller does not have the required role, the field will not be updated. Attempt to update inaccessible fields will result in failed update
  *  insert: If the caller does not have the required role, attempt to set field value during insertion will result in failed insert.

* `find`: Array of roles allowed to return the field in a find request.  If the caller does not have the required role, the field is removed from the response. [optional]
* `insert`: Array of roles allowed to insert (set) a new value for the field.  If the caller does not have the required role the insert request will fail. [optional]
* `update`: Array of roles allowed to update a field.  If the caller does not have the required role the update request will fail. [optional]

## Special Roles
The same special roles apply to field access.  See [Special Roles](access.html#special-roles) in the entity access documentation.
