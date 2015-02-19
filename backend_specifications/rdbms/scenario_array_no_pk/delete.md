# delete

## lightblue request
```
POST /data/delete/arrayNoPk/0.1.0
```
```json
{
    "objectType": "arrayNoPk",
    "version": "0.1.0",
    "query": {
        "field": "id",
        "op": "=",
        "rvalue": "123456"
    }
}
```

## generated SQL
```sql
DELETE FROM ARRAY_STRING_WITHOUT_PK
WHERE BASE_ID=123456;

DELETE FROM BASE
WHERE ID=123456;
```

## lightblue response
```json
{
    "status": "complete",
    "modifiedCount": 1,
    "matchCount": 1
}
```
