# DELETE: Release logical lock

Releases a lock to a logical resource which was acquired before by
the same caller.


### Request

The request contains the locking domain, resource identifier, and the
caller identifier. The locking domain is configured for backends and
determines the data store used to keep locking records. The resource
identifier is a logical identifier to the resource to be locked. The
caller identifier is a string uniquely identifying the caller.

```
DELETE /data/lock/{domain}/{callerId}/{resourceId}
```

### Response: Success
If release is successful, returns
```javascript
{result: true}
```

### Response: Errors
mongo-crud:InvalidLockingDomain - Invalid domain

