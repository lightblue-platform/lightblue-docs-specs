# Data

## Data APIs

All Data APIs get a Json request object, and return a Json response
object.

### Common: Request

```javascript
{
  "entity": entityName,
  "entityVersion": entityMetadataVersion,
  "client": clientIdentification,
  "execution": executionOptions
}
```

* `entity`: Mandatory field that specifies the name of the
  entity to be operated on
* `entityVersion`: Optional field that gives the version of
  metadata. If not given, latest version is assumed.
* `clientId`: Optional field. This field contains the authentication
  information for the caller, which is auth implementation
  specific, or a session identifier obtained from an earlier call.
* `execution`: Optional [execution](execution.md) flags.


### Common: Response

```javascript
{
  "status": one of complete, partial, async, or error,
  "modifiedCount": int,
  "matchCount": int,
  "taskHandle": sync
  "session": sessionInfo,
  "processed": [ entityData ]
  "dataErrors": [ { "data": entityData, "errors": [ errors ] } ]
  "errors": [ errors ]
}
```
* `status`: enumerated field
  * `COMPLETE`: The operation completed, all requests are processed
  * `PARTIAL`: Some of the operations are successful, some failed
  * `ASYNC`: Operation running asynchronously
  * `ERROR`: Operation failed because of some system error
* `modifiedCount`: number of entities affected by the call,
  if this is an insert, update, or delete
* `matchCount`: total number of entities in the result set,
  if this is a find.
* `taskHandle`: Only present if aysnchronous operation is
  requested, and status=async. Contains the task handle that
  the client can use to retrieve status information about the
  ongoing execution
* `session`: Session information for the client. How this is
  used is implementation dependent, and TBD
* `processed`: Projected entity information for processed entities.
  Only for insert and update. Contains only successfully updated
  entities. It is up to the caller to project identifying fields
  if caller needs.
* `dataErrors`: All entity related errors.
  * `data`: Projected entity data
  * `errors`: Array of errors for that particular object
* `errors`: Errors not related to data


### Insert

Request object:
```javascript
{
  common stuff,
  "data": [ entityData ],
  "projection": projection
}
```
* `data`: Array of entity objects
  * If object ids are given, entities will be inserted
    with the given object id, otherwise object id will be auto-generated
* `projection`: A [projection](projection.md) expression specifying what fields of entities
  to return once the insertion is performed.

### Save

Updates or inserts entities with the given ID.

Request object:
```javascript
{
  common stuff,
  "data": [ entityData ],
  "upsert": boolean,
  "projection": projection
}
```
* `data`: Array of entity objects
  * Objects IDs must be given for objects to be updated
  * If an object with the same ID exists, that object is updated.
    Otherwise, if upsert is true, that object is inserted, if not,
    error is returned.
* `upsert`: If true, inserts the doc if not found, or if the doc
  doesn't have id
* `projection`: A [projection](projection.md) expression specifying what fields of updated
  entities to return

### Update

Partially updates entities matching a search criteria

Request object:
```javascript
{
  common stuff,
  "query": query_expression,
  "update": update_expression,
  "projection": projection
}
```
* `query`: A [query](query.md) expression determining which documents to update
* `update`: An [update](update.md) expression
* `projection`: A [projection](projection.md) expression specifying what fields of updated
  entities to return

### Delete

Request object:
```javascript
{
  common stuff,
  "query": query_expression
}
```

### Find

Request object:
```javascript
{
  common stuff,
  "query": query_expression,
  "projection": projection,
  "sort": sort,
  "range": [ from, to ],
  "from": from,
  "to": to
}
```
* `query`: A [query](query.md) expression, or null to return everything
* `projection`: A [projection](projection.md) expression specifying what to return.  If not set, nothing is returned.
* `sort`: Optional [sort](sort.md) specification
* `range`: Optional [range](range.md), inclusive range of results to return.
* `from`: Optional beginning of a [range](range.md), use only if 'range' is omitted.
* `to`: Optional end of a [range](range.md), use only if 'range' is omitted.

If [sort](sort.md) is not given, the results will be returned in an
unspecified order, making [range](range.md) useless. There is no guarantee
that subsequent calls will retrieve the results in the same order.

