# insert

## lightblue request
```
PUT /data/insert/myMetadata/0.1.0
```
```json
{
    "objectType": "myMetadata",
    "version": "0.1.0",
    "data": [
        {
            "date": "20150217 1721000000-0500",
            "number": "123456",
            "varchar2": "hi there"
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
INSERT INTO MY_TABLE (MY_DATE, MY_NUMBER, MY_VARCHAR2)
VALUES (to_date('20150217 222100', 'YYYYMMDD HH24MISS'), 123456, 'hi there');
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
            "varchar2": "hi there"
        }
    ]
}  
```
