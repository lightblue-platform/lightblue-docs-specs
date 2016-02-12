# container field
There are three types of `type` values:
* simple
    * bigdecimal, biginteger, boolean, binary, date, double, integer, string
* container
    * array, object
* reference
    * reference

This section is focusing on the container types.

## object


## array

### Predefined Fields

The following fields are stored with every entity. If the metadata
defines these as fields, they are exposed to the callers. Otherwise,
these fields will not be exposed.


*  &lt;arrayField&gt;#: arrayField is an array field of the entity. The variable &lt;arrayField&gt;# keeps the number of elements in the array
  * Type: int
  * read-only

* object_type: Entity type
  * Type: string
  * read-only
