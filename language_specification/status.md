# status
Schema status.

```javascript
"status" : {
    "log": [
        {
            "date": date,
            "value": String,
            "comment": String
        },
        ...
    ],
    "value": "active"
}
```

* `log`: History of status changes over time. [optional]
    * `date`: Date of value change.
    * `value`: Status value when changed.
    * `comment`: Comment about change in status.
* `value`: Status of this schema [required].  One of:
    * `active`: Schema can be used and is **recommended** for new clients
    * `deprecated`: Schema can be used but is **not recommended** for new clients.  Existing clients should transition to an active version of the metadata.
    * `disabled`: Schema **cannot be used**.
