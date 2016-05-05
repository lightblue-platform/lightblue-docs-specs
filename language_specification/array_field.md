# reference field
There are three types of `type` values:
* simple
    * `bigdecimal`, `biginteger`, `boolean`, `binary`, `date`, `double`, `integer`, `string`
* container
    * `array`, `object`
* reference
    * `reference`

This section is focusing on the reference type.

## reference
Reference fields allow for associating entities in lightblue, similar to joins in a relational database.  A few key things to remember about references:
* References are defined in metadata, meaning there is no support for ad-hoc joins.
* References are **not projected by default**.  To fetch a reference you must explicitly project the reference field.

```javascript
"referenceFieldName": {
    "type": "reference",
    "entity": "referencedEntity",
    "versionValue": "referencedEntityVersion",
    "query": { ... }
}
```

* `referenceFieldName`: Name of the field to contain the reference.  When projected, **this field will always have a `type` of `array`**.[required]
* `entity`: The name of the entity being reference. [required]
* `versionValue`: The version of the entity being referenced. [required]
* `query`: The query used to retreive the referenced entity.

The `query` is written from the referenced entity point of view.  To use a field in the referencing entity use the special keyword `$parent`.

### Example
You have a `user` entity that references an `address`.  The `address` has a `userId` field that is the foreign key to `_id` in `user`.

The `address` metadata:
```javascript
{
    "entityInfo": {
        "name": "address",
        ...
    },
    "schema": {
        "name": "address",
        "version": {
            "changelog": "initial version",
            "value": "1.0.0"
        },
        "fields": {
            "_id": {
                "type": "string",
                "constraints": {
                    "identity": true
                }
            },
            "userId": {
                "type": "string"
            },
            ...
        }
    }
}
```

The `user` metadata, with reference to `address`:
```javascript
{
    "entityInfo": {
        "name": "user",
        ...
    },
    "schema": {
        "name": "user",
        "version": {
            "changelog": "initial version",
            "value": "1.0.0"
        },
        "fields": {
            "_id": {
                "type": "string",
                "constraints": {
                    "identity": true
                }
            },
            "addresses": {
                "type": "reference",
                "entity": "address",
                "versionValue": "1.0.0",
                "query": {
                    "field": "userId",
                    "op": "=",
                    "rfield": "$parent._id"
                }
            },
            ...
        }
    }
}
```

In this example you see the `addresses` field in `user` that is a reference to `address`. In that query:
* `field` refers to `userId` in the `address` entity
* the operation is `=`
* and the right-hand field is `_id` in `user`

How to read `rfield`:  from `addresses` go up one (`$parent`) and then find `_id`.

To query for all users with `_id` equal to `10` and project all the user's fields and referenced addresses:
```javascript
{
    "objectType": "user",
    "projection": [
        {
            "field": "*",
            "include": true,
            "recursive": true
        },
        {
            "field": "addresses",
            "include": true,
            "recursive": true
        }
    ],
    "query": {
        "field": "_id",
        "op": "=",
        "rvalue": "10"
    }
}
```
