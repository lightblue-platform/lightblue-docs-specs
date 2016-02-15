# hooks

```javascript
"hooks": [
    {
        "actions": [ ... ],
        "configuration": { ... },
        "name": "hookName",
        "projection": { ... }
    },
    ...
]
```

* `actions`: Array of actions when the hook is applied.  One or more of: `insert`, `update`, `find`, `delete` [required]
* `configuration`: Hook specific configuration details.  See documentation for specific hook. [optional]
* `name`: Hook implementation name. [required]
* `projection`: Projection applied against both request and response data. See CRUD spec for details. [optional]
