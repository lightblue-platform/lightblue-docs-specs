# find

## lightblue request
```
POST /data/find/myMetadata/0.1.0
```
```json
{
    "objectType": "myMetadata",
    "version": "0.1.0",
    "query": {
        "field": "number",
        "op": "=",
        "rvalue": "123456"
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
SELECT *
FROM MY_TABLE
WHERE MY_NUMBER=123456;
```

## lightblue response
```json
{
    "status": "complete",
    "matchCount": 1,
    "processed": [
        {
            "date": "20150217 222100",
            "number": "123456",
            "varchar2": "hi there",
            "char": "boo!",
            "long": "A long time ago in a galaxy far, far away...."
        }
    ]
}
```
