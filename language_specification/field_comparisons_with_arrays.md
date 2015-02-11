# Field Comparisons with Arrays
When comparing fields using the field comparison expression it is possible one or more of the fields is an array.  This section's intent is to make it very clear what happens with each operation when both fields are arrays.

A quick note on operation equality.  The following pairs of binary comparison operators are equal:

| symbol | text |
| ------ | ---- |
| = | $eq |
| != | $neq |
| < | $lt |
| > | $gt |
| <= | $lte |
| >= | $gte |




## Array1 = Array2

```javascript
    {
        "field": "array1",
        "op": "$eq",
        "rvalue": "array2"
    }
```

This expression is true when all of the following are true:
* both array fields are of the same length
* for `i` between `0` and the array length - 1
    * `array1[i] == array2[i]`

## Array1 != Array2

```javascript
    {
        "field": "array1",
        "op": "$neq",
        "rvalue": "array2"
    }
```

This expression is true when any of the following is true:
* array fields have different lengths
* there exists at least one `i` such that `array1[i] != array2[i]`

## Array1 < Array2

```javascript
    {
        "field": "array1",
        "op": "$lt",
        "rvalue": "array2"
    }
```

This expression is true when all of the following are true:
* `array1.length <= array2.length`
* for `i` between `0` and the array length - 1
    * `array1[i] < array2[i]`

## Array1 > Array2

```javascript
    {
        "field": "array1",
        "op": "$gt",
        "rvalue": "array2"
    }
```

This expression is true when all of the following are true:
* `array1.length >= array2.length`
* for `i` between `0` and the array length - 1
    * `array1[i] > array2[i]`

## Array1 <= Array2

```javascript
    {
        "field": "array1",
        "op": "$lte",
        "rvalue": "array2"
    }
```

This expression is true when all of the following are true:
* length of `array1` is less than or equal to length of `array2`
* for `i` between `0` and the array length - 1
    * `array1[i] <= array2[i]`

## Array1 >= Array2

```javascript
    {
        "field": "array1",
        "op": "$gte",
        "rvalue": "array2"
    }
```

This expression is true when all of the following are true:
* length of `array1` is greater than or equal to length of `array2`
* for `i` between `0` and the array length - 1
    * `array1[i] >= array2[i]`
