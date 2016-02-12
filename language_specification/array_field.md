# reference field
There are three types of `type` values:
* simple
    * bigdecimal, biginteger, boolean, binary, date, double, integer, string
* container
    * array, object
* reference
    * reference

This section is focusing on the reference type.



## reference

* reference: If type=object or array. If reference is present, fields and elements cannot be given. Determines a query, projection, and sort order for another entity to be inserted into the document
```javascript
    "reference" : {
      "entity": entityName,
      "versionValue": versionValue,
      "projection": projection_expression,
      "query": query_expression,
      "sort": sort
    }
```


   * references: denotes fields in other entities this field depends on. The constraint requires that if this field value is non-null, there must be an instance of entity with { entityField: thisField }
```javascript
    {
        "references": {
           "entityName": entityName,
           "versionValue": version.value
           "entityField": fieldName
        }
    }
```




### fields_object



* fields: If type=object, an array of field objects
* items: If type=array, an object of the form:
```javascript
    "items" : {
      "type": type
      "fields" : { ... } (if type=object)
    }
```



