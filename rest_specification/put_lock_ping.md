# PUT: PING logical lock

Pings a logical resource lock, extending its lifetime. Also acts as a
check to see if lock is still valid.

### Request

The request contains the locking domain, resource identifier, and the
caller identifier. The locking domain is configured for backends and
determines the data store used to keep locking records. The resource
identifier is a logical identifier to the resource to be locked. The
caller identifier is a string uniquely identifying the caller.

```
PUT /data/lock/{domain}/{resourceId}/{callerId}/ping
```

### Response: Success
If successful, returns
```javascript
{result: <n> }
```
Returns error if lock is invalid, or does not belong to the caller.

### Response: Errors
mongo-crud:InvalidLockingDomain - Invalid domain

