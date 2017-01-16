# simple field
There are three types of `type` values:
* simple
    * `bigdecimal`, `biginteger`, `boolean`, `binary`, `date`, `double`, `integer`, `string`
* container
    * `array`, `object`
* reference
    * `reference`
* free-form
    * `any`

This section is focusing on the simple types.

## bigdecimal & biginteger
If a field is defined as biginteger or bigdecimal in metadata it is stored as a string on the back end.  Therefore **operators &lt; and &gt; are not allowed during search**.

## boolean
Values can be `true` or `false`.

## binary
An **open-ended data type for storing binary data**, it is recommended that each use of this field clearly indicates what encoding is used.  For example, in java use [DatatypeConverter.printBase64Binary(byte[])](http://www.w3.org/TR/xmlschema-2/#base64Binary).  Note there are limits on document sizes, see [MongoDB Limits and Thresholds](http://docs.mongodb.org/manual/reference/limits/#BSON-Document-Size) documentation.

## date
JSON does not have a standard format for date.  In lightblue we are using the following [format](http://docs.oracle.com/javase/7/docs/api/java/text/SimpleDateFormat.html): `YYYYMMDDTHHmmssSSSS+-ZZZZ`

For example, to represent noon on Janurary 1, 2016 EST: `20160101T1200000000-0500`

## double
Floating point number.  If precision is important use `bigdecimal`.

## integer
A 64 bit integer.

## string
Any field with text.  Limits on size can be imposed using constraints: `minLength`, `maxLength`

## uid
Exists but is deprecated.  **Do not use!**  Instead use `type` of `string` and a [value generator](http://docs.lightblue.io/cookbook/value_generators.html).  The following example is a 1:1 replacement for the old `uid` type where it also implied `identity` constraint.

```javascript
"fieldName": {
    "type": "string",
    "constraints": {
        "identity": true
    },
    "valueGenerator": {
        "type": "UUID"
    }
}
```
