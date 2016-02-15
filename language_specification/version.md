# version
The version data for the schema.

```javascript
"version" : {
    "changelog": "Why this version was created.",
    "extendVersions": [
        {
            "0.1.0"
        },
        ...
    ],
    "value": "0.2.0-SNAPSHOT",
}
```

* `changelog`: Text describing the version.  [required]
* `extendVersions`: Array of version values this version extends (implies merges) [optional]
* `value`: The version string.  See [Versioning](http://docs.lightblue.io/standards/versioning.html) standard. [required]


