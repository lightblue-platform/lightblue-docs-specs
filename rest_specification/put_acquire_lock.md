# PUT: Acquire logical lock

Attempts to acquire a lock to a logical resource.


### Request

The request contains the locking domain, resource identifier, and the
caller identifier. The locking domain is configured for backends and
determines the data store used to keep locking records. The resource
identifier is a logical identifier to the resource to be locked. The
caller identifier is a string uniquely identifying the caller. ttl is
optional, and gives the expiration duration for the lock in
milliseconds.

```
PUT /data/lock/{domain}/{callerId}/{resourceId}?ttl=<ttl>
```

### Response: Success
If locking is successful, returns
```javascript
{result: true}
```

### Response: Errors
mongo-crud:InvalidLockingDomain - Invalid domain

