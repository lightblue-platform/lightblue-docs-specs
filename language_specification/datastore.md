# datastore
Datastore properties are all determined by the backend.

```javascript
"datastore" : {
    "backend" : "backendName"
}
```

* `backend`: The name of the controller for the datastore.  Available controllers at this time are `mongo` and `ldap`. [required]

## backend = mongo

```javascript
"datastore" : {
    "backend" : "mongo",
    "collection" : "collection name"
    "datasource" : "logical name for the datasource",
}
```

* `backend`: The name for the mongo controller: `mongo` [required]
* `collection`: The name of the mongo collection in which the data is persisted. [required]
* `datasource`: The name of the datastore as defined in `datastore.json` config file. [required]
