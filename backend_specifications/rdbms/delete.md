# delete

## lightblue request
```
POST /data/delete/myMetadata/0.1.0
```
```json
{
    "objectType": "myMetadata",
    "version": "0.1.0",
    "query": {
        "field": "number",
        "op": "=",
        "rvalue": "123456"
    }
}
```

## generated SQL
```sql
DELETE FROM MY_TABLE
WHERE MY_NUMBER=123456;
```

## lightblue response
```json
{
    "status": "complete",
    "modifiedCount": 1,
    "matchCount": 1
}
```
