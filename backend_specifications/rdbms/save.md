# save
Change some of array contents.

## lightblue request
```
POST /data/save/arrayNoPk/0.1.0
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
                "apple",
                "three",
                "banana"
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
DELETE FROM ARRAY_STRING_WITHOUT_PK
WHERE BASE_ID=123456
AND S_FIELD NOT IN ('one', 'apple', 'three', 'banana');

INSERT INTO ARRAY_STRING_WITHOUT_PK (BASE_ID, S_FIELD)
WITH VALUE_LIST AS (
    SELECT 'one' as S_FIELD FROM DUAL
    UNION
    SELECT 'apple' as S_FIELD FROM DUAL
    UNION
    SELECT 'three' as S_FIELD FROM DUAL
    UNION
    SELECT 'banana' as S_FIELD FROM DUAL
)
SELECT 123456, B.S_FIELD
FROM ARRAY_STRING_WITHOUT_PK A,
VALUE_LIST B
WHERE A.S_FIELD(+)=B.S_FIELD
AND A.S_FIELD IS NULL;
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
                "apple",
                "three",
                "banana"
            ]
        }
    ]
}
```
