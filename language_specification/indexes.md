# indexes
Each index is an array of fields to index.

```javascript
"indexes": [
    {
        "fields": [
            {
                "field": "fieldName",
                "dir":"$asc",
                "caseInsensitive": false
            },
            ...
        ],
        "name": "indexName",
        "unique": true
    },
    ...
]
```

* `fields`: Array with field definitions that are part of the index. [required]
    * `field`: the path for the field in the index, do not include wildcard for arrays [required]
    * `dir`: direction of the index, `$asc` or `$desc` [required]
    * `caseInsensitive`: Optional flag to indicate if index is case insensitive.  Defaults to `false`. [optional]
* `name`: Name of the index.  Default set based on controller implementation. [optional]
* `unique`: Flag to indicate if index is a unique constraint.  Defaults to `false`. [optional]
