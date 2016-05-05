# Metadata

## Entity Metadata
Entity metadata are stored in a collection named "metadata". Format of metadata is:

```javascript
{
    "entityInfo": {
        "defaultVersion": "0.0.1",
        "name": "entityName",
        "datastore":  { ... },
        "enums": [ ... ],
        "hooks": [ ... ],
        "indexes": [ ... ],
    },
    "schema": {
        "name": "entityName",
        "access": { ... },
        "status": { ... },
        "version": { ... },
        "fields": { ... }
    }
}
```

* `entityInfo`: Data about an entity that is not versioned.
* `schema`: Data about an entity that is versioned.
