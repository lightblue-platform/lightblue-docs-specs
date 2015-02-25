# Data Types
Data types break down into the following main categories.  For each backend there are potentially many underlying datatypes that can represent these higher level concepts:
* Binary
* Boolean
* Date
* Numeric
    * Integer
    * Float
* String

## RDBMS, General
There is some overlap between vendors but it's not worth calling out separately. This section exists to be consistent with other sections and to explain this briefly.

## RDBMS, Oracle 11g
Source: http://ss64.com/ora/syntax-datatypes.html
* Binary
    * RAW
    * LONG RAW
    * MLSLABEL
    * BLOB
    * BFILE
* ~~Boolean~~
* Date
    * DATE
    * TIMESTAMP
    * WITH {LOCAL} TIMEZONE
    * INTERVAL YEAR (year_precision) TO MONTH
    * INTERVAL DAY (day_precision) TO SECOND
* Numeric
    * NUMBER
    * BINARY_FLOAT
    * BINARY_DOUBLE
* String
    * VARCHAR2
    * NVARCHAR2
    * CHAR
    * NCHAR
    * LONG
    * ROWID
    * UROWID
    * CLOB
    * NCLOB
    * XMLTYPE

## RDBMS, MySQL 5.5
Source: http://dev.mysql.com/doc/refman/5.5/en/data-types.html
* Binary
    * BLOB
* ~~Boolean~~
* Date
    * DATE
    * TIME
    * DATETIME
    * TIMESTAMP
    * YEAR
* Numeric
    * INTEGER, synonyms: INT
    * SMALLINT
    * DECIMAL, synonyms: DEC, FIXED
    * NUMERIC
    * FLOAT, synonyms: REAL (REAL_AS_FLOAT enabled)
    * DOUBLE PRECISION, synonyms: DOUBLE, REAL (REAL_AS_FLOAT disabled)
* String
    * CHAR
    * VARCHAR
    * BINARY
    * VARBINARY
    * TEXT
    * ENUM
    * SET

## RDBMS, MariaDB 10
Source: https://mariadb.com/kb/en/mariadb/data-types/
* Binary
    * TINYBLOB
    * BLOB
    * MEDIUMBLOB
    * LONGBLOB
* Boolean
    * BOOLEAN
* Date
    * DATE
    * TIME
    * DATETIME
    * TIMESTAMP
    * YEAR
* Numeric
    * TINYINT, synonyms: BOOLEAN
    * SMALLINT
    * MEDIUMINT
    * INT, synonyms: INTEGER
    * BIGINT
    * DECIMAL, synonyms: DEC, NUMERIC, FIXED
    * FLOAT
    * DOUBLE, synonyms: REAL, DOUBLE PRECISION
    * BIT
* String
    * CHAR
    * VARCHAR
    * BINARY, synonyms: CHAR BYTE
    * VARBINARY
    * TINYTEXT
    * TEXT
    * MEDIUMTEXT
    * LONGTEXT
    * ENUM
    * SET

## lightblue 1.0
Details what lightblue data types map to the higher level concepts documented earlier. Source: https://github.com/lightblue-platform/lightblue-core/blob/master/metadata/src/main/resources/json-schema/metadata/schema.json
* Binary
    * binary
* Boolean
    * boolean
* Date
    * date
* Numeric
    * integer
    * double
    * biginteger
    * bigdecimal
* String
    * string
    * uid
