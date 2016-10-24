# indexes
Each index is an array of fields to index.

```json
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
        "unique": true,
        "partialFilterExpression": {"field": {"$gt": 5}}
    },
    ...
]
```

* `fields`: Array with field definitions that are part of the index. [required]
    * `field`: the path for the field in the index, do not include wildcard for arrays [required]
    * `dir`: direction of the index, `$asc` or `$desc` [required]
    * `caseInsensitive`: Optional flag to indicate if index is case insensitive.  Defaults to `false`. [optional]
        * There are pros and cons to consider when using this flag.  Please read the [user docs](http://docs.lightblue.io/cookbook/case_insensitive_indexes.html) before using them. 
* `name`: Name of the index.  Default set based on controller implementation. [optional]
* `unique`: Flag to indicate if index is a unique constraint.  Defaults to `false`. [optional]
* `partialFilterExpression`: Mongo query used to define partial index. This is specific to Mongo. [optional]
