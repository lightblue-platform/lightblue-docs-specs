# update
Append a value to the array.

## lightblue request
```
POST /data/update/arrayNoPk/0.1.0
```
```json
{
    "objectType": "arrayNoPk",
    "version": "0.1.0",
    "query": {
        "field": "id",
        "op": "=",
        "value": "123456"
    },
    "update": {
        "$append": {
            "arrayStringWithoutPk": [
                "four"
            ]
        }
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
INSERT INTO ARRAY_STRING_WITHOUT_PK (BASE_ID, S_FIELD)
VALUES (123456, 'four');
```

## lightblue response
```json
{
    "status": "complete",
    "modifiedCount": 1,
    "matchCount": 1,
    "processed": [
        {
            "id": "123456",
            "a": "b",
            "arrayStringWithoutPk": [
                "one",
                "two",
                "three",
                "four"
            ]
        }
    ]
}
```
