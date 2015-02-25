# Field Mappings

## Simple
Hopefully this will make sense.. since this document is evaluating several RDBMS vendors it gets tricky to manage all the ways things could be mapped.  The following table outlines what RDBMS data types each lightblue data type must support mapping into and out of (bidirectional).  These are the obvious direct mappings.  Synonyms are excluded for simplicity.  We'll get into translations later.

|lightblue|Oracle 11g|MySQL 5.5|MariaDB 10|
|-|-|-|-|
|binary|RAW<br/>LONG RAW<br/>MLSLABEL<br/>BLOB<br/>BFILE|BLOB|TINYBLOB<br/>BLOB<br/>MEDIUMBLOB<br/>LONGBLOB|
|boolean|||TINYINT|
|date|DATE<br/>TIMESTAMP<br/>INTERVAL|DATE<br/>TIME<br/>TIMESTAMP<br/>YEAR|DATE<br/>TIME<br/>TIMESTAMP<br/>YEAR|
|integer<br/>biginteger|NUMBER<br/>PLS_INTEGER<br/>BINARY_INTEGER|INTEGER<br/>SMALLINT<br/>NUMERIC|TINYINT<br/>SMALLINT<br/>MEDIUMINT<br/>INT<br/>BIGINT<br/>BIT|
|double<br/>bigdecimal|BINARY_FLOAT<br/>BINARY_DOUBLE|DECIMAL<br/>FLOAT<br/>DOUBLE PRECISION|DECIMAL<br/>FLOAT<br/>DOUBLE|
|string<br/>uid|VARCHAR2<br/>NVARCHAR2<br/>CHAR<br/>NCHAR<br/>LONG<br/>ROWID<br/>UROWID<br/>CLOB<br/>NCLOB<br/>XMLTYPE|CHAR<br/>VARCHAR<br/>BINARY<br/>VARBINARY<br/>TEXT<br/>ENUM<br/>SET|CHAR<br/>VARCHAR<br/>BINARY<br/>VARBINARY<br/>TINYTEXT<br/>TEXT<br/>MEDIUMTEXT<br/>LONGTEXT<br/>ENUM<br/>SET



## Complex
This section is about translating data types from on type to another.  Instead of attempting to map each individual data type this section categories them based on the general data types with one variation: numeric is split into integer and float sub-types.

Rows are read as the "from", columns are the "to".  An "Y" indicates the mapping is possible, but makes no guarantee about it being a loss-less conversion.

|FROM v / TO >|binary|boolean|date|integer|float|string|
|-|-|-|-|-|-|-|
|binary|Y|Y|Y|Y|Y|Y|
|boolean|Y|Y|**N**|Y|Y|Y|
|date|Y|**N**|Y|Y|**N**|Y|
|integer|Y|Y|Y|Y|Y|Y|
|float|Y|Y|**N**|Y|Y|Y|
|string|Y|Y|Y|Y|Y|Y|

## Transformations
**This section is not complete!**

Transformations for data mappings may be necessary.  This section documents only cases where a transformation is necessary and what transform is applied in each direction. The variable `$value` indicates the source data.

|lightblue|Oracle|lightblue->Oracle|Oracle->lightblue|
|-|-|-|-|
|date|DATE|to_date(substr('$value', 0, 8), 'YYYYMMDD')|to_char($value, 'YYYYMMDD') + '0000000000-0000'|
|date|TIMESTAMP|to_date('$value', 'YYYYMMDD HH24MISSFF4-TZH')|to_char($value, 'YYYYMMDD HH24MISSFF4-TZH')|
|boolean|NUMBER|case when '$value'=='true' then 1 else 0 end|case when '$value'==1 then 'true' else 'false' end|

