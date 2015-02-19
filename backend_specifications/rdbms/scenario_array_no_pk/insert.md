# insert

## lightblue request
```
PUT /data/insert/arrayNoPk/0.1.0
```
```json
{
    "objectType": "arrayNoPk",
    "version": "0.1.0",
    "data": [
        {
            "id": "123456",
            "a": "b",
            "arrayStringWithoutPk": [
                "one",
                "two",
                "three"
            ]
        }
    ],
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
INSERT INTO BASE (ID, A_FIELD)
VALUES (123456, 'b');

INSERT INTO ARRAY_STRING_WITHOUT_PK (BASE_ID, S_FIELD)
VALUES (123456, 'one');

INSERT INTO ARRAY_STRING_WITHOUT_PK (BASE_ID, S_FIELD)
VALUES (123456, 'two');

INSERT INTO ARRAY_STRING_WITHOUT_PK (BASE_ID, S_FIELD)
VALUES (123456, 'three');
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
                "three"
            ]
        }
    ]
}
```
