# schema

Data about an entity that is versioned.

```javascript
"schema": {
    "name": "entityName",
    "access": { ... },
    "status": { ... },
    "version": { ... },
    "fields": { ... }
}
```

* `name`: Name of the entity.  Name is the same in entityInfo and schema sections. [required]
* `version`: The version data for the metadata. [required]
* `access`: Entity access definitions. Determines the access rights at an entity level. [optional]
* `status`: Entity metadata status. [required]
* `version`: The version data for the schema. [required]
* `fields`: Defines the model for the entity. [required]

