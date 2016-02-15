# enums
There are two ways enums can be defined, either as "simple" or "annotated".  You can mix these styles in your metadata.

## Simple enums
Allows you to define enum with simple values that do not have a description.  If descriptions are necessary you can use the "annotated" style of defining enums.

```javascript
"enums": [
    {
        "name": "enumName",
        "values": [
            "enum value 1",
            "enum value 2",
            ...
        ]
    },
    ...
]
```

* `name`: Name of the enumeration.  Must be unique to an individual entity's entityInfo.  This means enum names can be reused in other metadata. [required]
* `values`:  Array of enum values. [optional, see 'annotated' enums]

## Annotated enums
Allows you to define enum values with a description per value.  If descriptions are not necessary you can use the "simple" style of defining enums.

```javascript
"enums": [
    {
        "annotatedValues": [
            {
                "description": "Enum value description",
                "name": "Name of enum value"
            },
            ...
        ],
        "name": "enumName"
    },
    ...
]
```

* `annotatedValues`: Array of annotated enum values. [optional, see 'simple' enums]
    * `description`: Description of the enum value. [required]
    * `name`: The name of the enum value. [required]
* `name`: Name of the enumeration.  Must be unique to an individual entity's entityInfo.  This means enum names can be reused in other metadata. [required]
