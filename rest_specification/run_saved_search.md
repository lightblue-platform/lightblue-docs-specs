# Saved Search APIs

These APIs deal with saved searches.

## Run Saved Search

```
GET /data/search/{entityName}/{searchName}?param1=value1&param2=value2&P={projection}&S={sort}&from=n&to=n
GET /data/search/{entityName}/{entityVersion}/{searchName}?param1=value1&param2=value2&P={projection}&S={sort}&from=n&to=n
POST /data/search/{entityName}/{searchName}?P={projection}&S={sort}&from=n&to=n
{
   "param1"="value1",
   "param2"="value2",
   ...
}
POST /data/search/{entityName}/{entityVersion}/{searchName}?P={projection}&S={sort}&from=n&to=n
{
   "param1"="value1",
   "param2"="value2",
   ...
}
```

All these APIs run the saved search named 'searchName' defined for the
entity 'entityName':'entityVersion'. If 'entityVersion' is ommitted,
the default version of the entity is used. The projection, sort, from,
and to arguments are optional, and if provided, override the ones
given in the saved search. 

### Response: Success
On success returns the result of the search.

### Response: Errors
* crud:NoAccess - caller doesn't have access required to save the document
* mongo-crud:NullProjection - projection in request is missing


## Get Saved Searches for an Entity

Returns the saved searches defined for an entity

```
GET /data/search/{entityName}?P={projection}&S={sort}
```

This API runs a 'find' operation on the searches defined for the
entity, and by default, returns all the fields of those searches
ordered by name. The returned fields and sort can be overridden by
providing the projection and sort arguments.
