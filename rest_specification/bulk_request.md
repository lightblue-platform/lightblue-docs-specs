# Bulk request
Perform a number of operations (lightblue requests) in a single http request.
```
POST /data/bulk
```

### Request:
Contains a list of requests. Each request has a sequence number that can be used to match the corresponding response.
```
 {
      "requests": [
          {
              "seq":0,
              "op": "FIND",
              "request": { request }
          }
      ]
 }
```

### Response: Success
```
{
  "responses": [
    "seq": 0,
    "response": { response }
  ]
}
```

Where response is a [response JSON document](https://raw.githubusercontent.com/lightblue-platform/lightblue-core/master/crud/src/main/resources/json-schema/response.json).

### Sample Request:
This is a sample bulk request that looks for two specific combinations of cheese and sauce choices on a pizza entity.
```
{
    "requests": [
        {
            "seq":0,
            "op": "find",
            "request": {
                "entity": "pizza",
                "entityVersion": "0.1.0",
                "query": { "$and" : [
                    {
                        "field": "cheese",
                          "op": "=",
                          "rvalue": "blend"
                    },
                    {
                        "field" : "sauce",
                        "op" : "=",
                        "rvalue" : "red"
                    }
                    ]
                },
                "projection": {
                    "field": "*",
                    "recursive": true
                },
                "range": [0,10]
            }
        },
        {
            "seq":1,
            "op": "find",
            "request": {
                "entity": "pizza",
                "entityVersion": "0.1.0",
                "query": { "$and" : [
                    {
                        "field": "cheese",
                          "op": "=",
                          "rvalue": "romano"
                    },
                    {
                        "field" : "sauce",
                        "op" : "=",
                        "rvalue" : "garlic"
                    }
                    ]
                },
                "projection": {
                    "field": "*",
                    "recursive": true
                },
                "range": [0,10]
            }
        }
    ]
 }
```
