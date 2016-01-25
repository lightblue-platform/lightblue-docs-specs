# Range
Ranges can be specified in several ways.
* from, to
* from, maxResults

## from, to
The value for `from` is required, `to` is optional.  When no value for `to` is specified all records following `from` are returned.  Values for `from` and `to` are a zero based index of the sorted output from lightblue.  The range is inclusive.  A few examples..

Get first 5 records:
```javascript
{
    ...
    "from": 0,
    "to": 4
}
```

Get 6th to 10th record:
```javascript
{
    ...
    "from": 5,
    "to": 9
}
```

Get all records skipping the first 100:
```javascript
{
    ...
    "from": 100
}
```

## from, maxResults
The value of `from` is required, `maxResults` is optional.  When no value for `maxResults` is specified all records following `from` are returned.  Value for `from` is a zero based index of the sorted output from lightblue.  A few examples..

Get first 5 records:
```javascript
{
    ...
    "from": 0,
    "maxResults": 5
}
```

Get 5 records starting with the 6th:
```javascript
{
    ...
    "from": 5,
    "maxResults": 5
}
```

Get all records skipping the first 100:
```javascript
{
    ...
    "from": 100
}
```

## DEPRECATED: range
Deprecated in favor of `from` and `to` / `maxResults`.

The value of `range` is an array with two integer values and is optional.  Values in the array are a zero based index of the sorted output from lightblue.  The first element in the array is set as the `from` value and the second element is `to`.  See "from, to" above.

Example to find first 5 records:
```javascript
{
    ...
    "range": [0,4]
}
```
