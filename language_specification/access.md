# access
Entity access definitions. Determines the access rights at an entity level.

```javascript
"access": {
    "delete": [
        "delete role 1",
        ...
    ],
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

If no access is defined then access defaults to `anyone`.

* `delete`: Array of roles allowed to delete instances of the entity. [optional]
* `find`: Array of roles allowed to find data in this entity. [optional]
* `insert`: Array of roles allowed to insert new entities. [optional]
* `update`: Array of roles allowed to update existing entities. [optional]

If the caller does not have the required role for the operation, a (not allowed) error entity will be returned.


## Special Roles
There are two special roles you can use in lightblue without any work.

* `anyone`: any authenticated user has access to the operation on the entity
* `noone`: no user (literally) has access to the operation on the entity
