# reference field
There are four types of `type` values:
* simple
    * `bigdecimal`, `biginteger`, `boolean`, `binary`, `date`, `double`, `integer`, `string`
* container
    * `array`, `object`
* reference
    * `reference`
* free-form
    * `any`

This section is focusing on the any type.

## any

'any' type fields store any JSON data. The values stored in an 'any'
field are not searchable or sortable. The value of an 'any' field can
be checked if it is null or not.
