# access

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
