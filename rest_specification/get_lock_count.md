# GET: Get logical lock count

Returns the number of times the lock is acquired by the caller.

### Request

The request contains the locking domain, resource identifier, and the
caller identifier. The locking domain is configured for backends and
determines the data store used to keep locking records. The resource
identifier is a logical identifier to the resource to be locked. The
caller identifier is a string uniquely identifying the caller.

```
GET /data/lock/{domain}/{callerId}/{resourceId}
```

### Response: Success
If successful, returns
```javascript
{result: <n> }
```
Returns error if lock is invalid, or does not belong to the caller.

### Response: Errors
mongo-crud:InvalidLockingDomain - Invalid domain

