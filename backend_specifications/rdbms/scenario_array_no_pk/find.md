# find

## lightblue request
```
POST /data/find/arrayNoPk/0.1.0
```
```json
{
    "objectType": "arrayNoPk",
    "version": "0.1.0",
    "query": {
        "field": "id",
        "op": "=",
        "rvalue": "123456"
    },
    "projection": [
        {
            "field": "*",
            "recursive": true,
            "include": true
        }
    ]
}
```

## generated SQL
```sql
SELECT *
FROM BASE
WHERE ID=123456;

SELECT *
FROM ARRAY_STRING_WITHOUT_PK
WHERE BASE_ID=123456;
```

## lightblue response
```json
{
    "status": "complete",
    "matchCount": 1,
    "processed": [
        {
            "id": "123456",
            "a": "b",
            "arrayStringWithoutPk": [
                "one",
                "apple",
                "three",
                "banana"
            ]
        }
    ]
}
```
