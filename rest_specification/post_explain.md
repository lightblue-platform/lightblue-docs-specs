# POST: Explain
Return explain plan for given find query.  See [Find](../language_specification/data.html#find) in the Language Spec for details of the document posted.  Note that if caller requests a field they are not authroized to access the request will not fail.  Instead the field will simply not be returned in the response.

### Request
Body of request is a JSON document matching the [request JSON schema](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/findRequest.json).

Explain request for given entity and version.
```
POST /data/explain/{entityName}/{version}
{request JSON document}
```
---

Explain request for given entity using default version.
```
POST /data/explain/{entityName}
{request JSON document}
```

### Response: Success
On success returns a [response JSON document](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/response.json).

### Response: Errors
* crud:NoAccess - caller doesn't have access required to save the document
* mongo-crud:NullProjection - projection in request is missing

### Example: explain find country

#### Request
```javascript
POST /rest/data/explain/country/1.0.0

{
    "objectType": "country",
    "version": "1.0.0",
    "query": {
        "field": "iso2Code",
        "op": "$eq",
        "rvalue": "US"
    }
}
```

#### Response
```javascript
{
    "matchCount": 1,
    "modifiedCount": 0,
    "processed": [{
        "mongo": {
            "plan": {
                "executionStats": {
                    "allPlansExecution": {},
                    "executionStages": {
                        "advanced": 208,
                        "executionTimeMillisEstimate": 0,
                        "inputStage": {
                            "advanced": 208,
                            "direction": "forward",
                            "docsExamined": 208,
                            "executionTimeMillisEstimate": 0,
                            "filter": {
                                "$and": {
                                    "0": {
                                        "objectType": {
                                            "$eq": "country"
                                        }
                                    },
                                    "1": {
                                        "objectType": {
                                            "$eq": "country"
                                        }
                                    }
                                }
                            },
                            "invalidates": 0,
                            "isEOF": 1,
                            "nReturned": 208,
                            "needTime": 1,
                            "needYield": 0,
                            "restoreState": 1,
                            "saveState": 1,
                            "stage": "COLLSCAN",
                            "works": 210
                        },
                        "invalidates": 0,
                        "isEOF": 1,
                        "nReturned": 208,
                        "needTime": 1,
                        "needYield": 0,
                        "restoreState": 1,
                        "saveState": 1,
                        "stage": "PROJECTION",
                        "transformBy": {
                            "_id": 1,
                            "createdBy": 1,
                            "creationDate": 1,
                            "iso2Code": 1,
                            "iso3Code": 1,
                            "lastUpdateDate": 1,
                            "lastUpdatedBy": 1,
                            "name": 1,
                            "objectType": 1
                        },
                        "works": 210
                    },
                    "executionSuccess": true,
                    "executionTimeMillis": 0,
                    "nReturned": 208,
                    "totalDocsExamined": 208,
                    "totalKeysExamined": 0
                },
                "ok": 1.0,
                "queryPlanner": {
                    "indexFilterSet": false,
                    "namespace": "data.country",
                    "parsedQuery": {
                        "$and": {
                            "0": {
                                "iso2Code": {
                                    "$eq": "US"
                                }
                            },
                            "1": {
                                "iso2Code": {
                                    "$eq": "US"
                                }
                            }
                        }
                    },
                    "plannerVersion": 1,
                    "rejectedPlans": {},
                    "winningPlan": {
                        "inputStage": {
                            "direction": "forward",
                            "filter": {
                                "$and": {
                                    "0": {
                                        "iso2Code": {
                                            "$eq": "US"
                                        }
                                    },
                                    "1": {
                                        "iso2Code": {
                                            "$eq": "US"
                                        }
                                    }
                                }
                            },
                            "stage": "COLLSCAN"
                        },
                        "stage": "PROJECTION",
                        "transformBy": {
                            "_id": 1,
                            "createdBy": 1,
                            "creationDate": 1,
                            "iso2Code": 1,
                            "iso3Code": 1,
                            "lastUpdateDate": 1,
                            "lastUpdatedBy": 1,
                            "name": 1,
                            "objectType": 1
                        }
                    }
                },
                "serverInfo": {
                    "gitVersion": "abcdefg",
                    "host": "lightbluemongo.lightblue.io",
                    "port": 27017,
                    "version": "3.2.4"
                }
            },
            "projection": {
                "_id": 1,
                "createdBy": 1,
                "creationDate": 1,
                "iso2Code": 1,
                "iso3Code": 1,
                "lastUpdateDate": 1,
                "lastUpdatedBy": 1,
                "name": 1,
                "objectType": 1
            },
            "query": {
                "$and": [{
                    "iso2Code": "US"
                }]
            }
        },
        "request": {
            "projection": {
                "field": "*",
                "include": true,
                "recursive": true
            },
            "query": {
                "field": "iso2Code",
                "op": "$eq",
                "rvalue": "US"
            }
        }
    }],
    "status": "COMPLETE"
}
```
