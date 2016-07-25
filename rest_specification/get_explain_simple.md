# GET: Explain (Simple)

A simplified explain API that takes just query parameters to do a simple search.

See [GET: Find (Simple)](get_find_simple.md) for query param details.

### Example: explain find country

#### Request
```javascript
GET /rest/data/explain/country/1.0.0?Q=iso2Code:US
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
