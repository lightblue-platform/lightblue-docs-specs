# Data Structures
Each solution has its own way of defining data structures. This section outlines each. Note that this is not an exhaustive list of features from each database vendor. Instead, this is a list of the features we use and are required to meet our business needs.

## RDBMS, General
The following structures of interest are common in all RDBMS vendors reviewed for this document:
* TABLE
* VIEW
* PRIMARY KEY / FOREIGN KEY

In general the following features are useful in an RDBMS system and are noted for each of the vendors:
* Paging - limit the result set returned by a minimum and maximum index
* Key Generation - used often for primary key generation, typically generates an integer in RDBMS
vendors
* Procedural Language - supported by some vendors, each one is different

## RDBMS, Oracle 11g
* Paging - using the ROWNUM pseudocolumn
    * [Ask Tom: On ROWNUM and Limiting Results](http://www.oracle.com/technetwork/issue-archive/2006/06-sep/o56asktom-086197.html)
* Key Generation - Oracle Sequences
    * [Managing Sequences](http://docs.oracle.com/cd/B28359_01/server.111/b28310/views002.htm#ADMIN11792)
* Procedural Language - [Oracle PL/SQL](http://www.oracle.com/technetwork/database/features/plsql/index.html)
    * Function
    * Procedure
    * Package

## RDBMS, MySQL 5.5
* Paging - use LIMIT in select statements
    * [MySQL :: MySQL 5.5 Reference Manual :: 13.2.9 SELECT Syntax](https://dev.mysql.com/doc/refman/5.5/en/select.html)
* Key Generation - AUTO_INCREMENT
    * [MySQL :: MySQL 5.6 Reference Manual :: 3.6.9 Using AUTO_INCREMENT](https://mariadb.com/kb/en/mariadb/sequence/)
* Procedural Language - supported but we do not use it
    * [MySQL :: MySQL 5.5 Reference Manual :: 13.1.15 CREATE PROCEDURE and CREATE
FUNCTION Syntax](http://dev.mysql.com/doc/refman/5.5/en/create-procedure.html)

## RDBMS, MariaDB 10
* Paging - use LIMIT in select statements
    * [LIMIT ROWS EXAMINED - MariaDB Knowledge Base](https://mariadb.com/kb/en/mariadb/limit-rows-examined/)
* Key Generation - Storage engine for Sequences
    * [Sequence - MariaDB Knowledge Base](https://mariadb.com/kb/en/mariadb/sequence/)
* Procedural Language `- supported but no plans to use it
    * [CREATE PROCEDURE - MariaDB Knowledge Base](https://mariadb.com/kb/en/mariadb/create-procedure/)

## lightblue 1.0
* JSON document with
    * sub-documents
    * arrays containing simple data types
    * arrays containing sub-documents
* Paging - supported with range in query language
    * must be mapped to how the backend implements paging
* Key Generation - uid support
    * uid is a string
