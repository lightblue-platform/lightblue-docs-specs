# update

## lightblue request
```
POST /data/update/myMetadata/0.1.0
```
```json
{
    "objectType": "myMetadata",
    "version": "0.1.0",
    "query": {
        "field": "number",
        "op": "=",
        "value": "123456"
    },
    "update": {
        "$set": {
            "char": "boo!"
        }
    },
    "projection": [
        {
            "field": "*",
            "include": true
        }
    ]
}
```

## generated SQL
```sql
UPDATE MY_TABLE
SET MY_CHAR='boo!'
WHERE MY_NUMBER = 123456;
```

## lightblue response
```json
{
    "status": "complete",
    "modifiedCount": 1,
    "matchCount": 1,
    "processed": [
        {
            "date": "20150217 222100",
            "number": "123456",
            "varchar2": "hi there",
            "char": "boo!"
        }
    ]
}
```
