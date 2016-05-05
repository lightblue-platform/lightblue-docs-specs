# GET: Generate value
Get generated values for a field 

### Request
Generate a value using value generators declared for the 'field' of the given entity version. If 'n' is given, generates that many values.
```
GET /data/generate/{entityName}/{version}/{field}?n={n}
```
Generate a value using value generators declared for the 'field' of the given entities default version. If 'n' is given, generates that many values.
```
GET /data/generate/{entityName}/{field}?n={n}
```
---

### Example:

Request:
```
GET /data/generate/test/1.0.0/path.*.to.field?n=3
```

Response:
```
{ "status":"COMPLETE",processed":["50000001","50000002","50000003"]}
```

### Response: Success
On success returns a [response JSON document](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/response.json), with "processed" field containing an array of generated values.

If the field does not have a generator, returns a successful response with empty "processed" field.

### Response: Error

* crud:NoAccess - caller doesn't have read, insert, or update access to the entity
