# save

## lightblue request
```
POST /data/save/myMetadata/0.1.0
```
```json
{
    "objectType": "myMetadata",
    "version": "0.1.0",
    "data": [
        {
            "date": "20150217 1721000000-0500",
            "number": "123456",
            "varchar2": "hi there",
            "char": "boo!",
            "long": "A long time ago in a galaxy far, far away...."
        }
    ],
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
SET MY_DATE=to_date('20150217 222100', 'YYYYMMDD HH24MISS'),
MY_VARCHAR2='hi there',
MY_CHAR='boo!',
MY_LONG='A long time ago in a galaxy far, far away....'
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
            "char": "boo!",
            "long": "A long time ago in a galaxy far, far away...."
        }
    ]
}
```
