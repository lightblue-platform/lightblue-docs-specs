# entityInfo
Data about an entity that is not versioned.

```javascript
"entityInfo": {
    "defaultVersion": "0.0.1",
    "name": "entityName",
    "datastore":  { ... },
    "enums": [ ... ],
    "hooks": [ ... ],
    "indexes": [ ... ],
}
```

* `defaultVersion`: Optional field that indicate the version of the entity used by default if no version is specified by the client.  Note that if this field is not set and the client does not supply a version the request will fail. [optional]
* `name`: Name of the entity.  Name is the same in entityInfo and schema sections. [required]
* `datastore`: Datastore object. The actual object implementation depends on the backend. [required]
* `enums`: Definition of enumerations (aka value sets) that are referenced from schema. [optional]
* `hooks`: Array of individual hook objects. [optional]
* `indexes`: Array of index information. [optional]
