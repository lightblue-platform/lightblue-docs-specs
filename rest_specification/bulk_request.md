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
