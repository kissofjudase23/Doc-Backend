# MySQL  

# Table of Contents
- [Data Types](#data-types)
- [Charset](#charset)
- [ACID Model（InnoDB Engine)](#acid-modelinnodb-engine)
- [Security](#security)
- [Normalization](#normalization)
- [Partitions](#partitions)
- [Stored Objects](#stored-objects)
- [Optimization](#optimization)
- [Profiling](#profiling)


## [Data Types](https://dev.mysql.com/doc/refman/8.0/en/data-type-overview.html)
  * [Numeric Type](https://dev.mysql.com/doc/refman/8.0/en/numeric-type-overview.html)
  * [Date and Time Type](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-type-overview.html)
  * [String Type](https://dev.mysql.com/doc/refman/8.0/en/string-type-overview.html)
  * [JSON](https://dev.mysql.com/doc/refman/8.0/en/json.html) (from mysql 5.7)
    * [How to create index on JSON](https://blog.gslin.org/archives/2016/03/09/6406/mysql-5-7-%E7%9A%84-json%E3%80%81virtual-column-%E4%BB%A5%E5%8F%8A-index/) 
      * Use [Virtual Column](https://dev.mysql.com/doc/refman/8.0/en/create-table-generated-columns.html)
        * create virtual column on the key street
            ```sql
            ALTER TABLE test_features ADD COLUMN street VARCHAR(30) GENERATED ALWAYS AS (json_unquote(json_extract(`feature`,'$.properties.STREET'))) VIRTUAL;
            ```
        * create an index on it- [MySQL](#mysql)

            ```sql
            ALTER TABLE test_features ADD KEY `street` (`street`);
            ```
## Charset
  * [Never use “utf8”. Use “utf8mb4”](https://medium.com/@adamhooper/in-mysql-never-use-utf8-use-utf8mb4-11761243e434)
    * MySQL's **utf8mb4** means **UTF-8**.
    * MySQL's **utf8** means a proprietary character encoding. This encoding can’t encode many Unicode characters.


## [ACID Model](https://dev.mysql.com/doc/refman/8.0/en/mysql-acid.html)（InnoDB Engine)
  * Atomicity
    * **Transactions** are atomic units of work that can be committed or rolled back. When a transaction makes multiple changes to the database, **either all the changes succeed when the transaction is committed, or all the changes are undone when the transaction is rolled back**.
  * Consistency
    * The database remains in a consistent state at all times, if related data is being updated across multiple tables, **queries see either all old values or all new values, not a mix of old and new values**. 
  * Isolation
    * Transactions are protected (isolated) from each other while they are in progress; they cannot interfere with each other or see each other's uncommitted data. 
    * [Isolation Level](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html)
      - READ UNCOMMITTED
      - READ COMMITTED
      - REPEATABLE READ
      - SERIALIZABLE
    * Durability
      * The results of transactions are durable: once a commit operation succeeds, the changes made by that transaction are safe from power failures, system crashes, race conditions, or other potential dangers that many non-database applications are vulnerable to. 
       
## Security
  * [SQL Injection](https://en.wikipedia.org/wiki/SQL_injection)
      * SQL injection is a code injection technique, used to attack data-driven applications, in which malicious SQL statements are inserted into an entry field for execution.
        ```sql
        SELECT * FROM users WHERE name = '' OR '1'='1';
        ```
    * solution:
      * Prepare statement, Parameter Binding
        * If you have any "special" characters (such as semicolons or apostrophes) in your data, they will be automatically quoted for you by the SQLEngine object, so you don't have to worry about quoting.
          ```python
          #!/bin/env python
          #-*- encoding: utf-8 -*-
          import time
          from sqlalchemy import text
          def insert_into_xxx_tbl(user_id, user_name, nickname):
              insert_params_dict = {
                  'user_id': user_id,
                  'user_name': user_name,
                  'nickname': nickname,
                  'db_insert_time': int(time.time()),
                  'db_update_time': int(time.time()),
              }

          ## use sqlalchemy bindparams to prevent sql injection
          pre_sql = 'insert into xxx_tbl (user_id, user_name, nickname, db_insert_time, db_update_time) values(:user_id, :user_name, :nickname, :db_insert_time, :db_update_time)'
          bind_sql = text(pre_sql)
          resproxy = _db_inst.connect().execute(bind_sql, insert_params_dict)

          ## return lastid as event_id
          event_id = resproxy.lastrowid
          ```
       * [Pure ORM](https://stackoverflow.com/questions/6501583/sqlalchemy-sql-injection/6501664)
         *  This also means that unless you deliberately bypass SQLAlchemy's quoting mechanisms, SQL-injection attacks are basically impossible.
         * Correct Example:
           ```python
           session.query(MyClass).filter(MyClass.foo==getArgs['val']))
           ```
         * Bad Example:
            ```python
            session.query(MyClass).filter("foo={}".format(getArgs['val']))
            ```
## [Normalization](https://medium.com/@habibul.hasan.hira/database-normalization-8cdaddbb7715)
  * 1NF
    * Every column of a table should be atomic. That means you **can not put multiple values in a database column**.
  * 2NF
    * If we have **composite primary key**! every non key field should be fully **depended on both key**. 
  * 3NF
    * When a database table is in second normal form , there should be no transitive functional dependency. That means any non primary key field should not be depend on other on primary key field.
  * BCNF
  * 4NF
  * 5NF

## [Partitions](https://dev.mysql.com/doc/refman/8.0/en/partitioning.html)
## [Stored Objects](https://dev.mysql.com/doc/refman/8.0/en/stored-objects.html)
  * Stored procedure
  * Trigger
  * Event
  * View
## [Optimization](https://dev.mysql.com/doc/refman/8.0/en/optimization.html)
  * Indexes:
    * Use good indexes
      * Columns that you are querying (**SELECT, GROUP BY, ORDER BY, JOIN**) could be faster with indices.
      * Indices are usually represented as **self-balancing B-tree** that keeps data sorted and allows searches, sequential access, insertions, and deletions in logarithmic time.
      * Writes could also be slower since the index also needs to be updated.
       
    * Use good **composite indexes**:
      * If certain fields tend to **appear together in queries**, then it’s a good idea to create a composite index on them. 
      * Similar to single indexes, the cardinality of the fields matters to the effectiveness composite indexes.
       
    * Avoid Unnecessary Indexes 
      * Do not use an index for **low-read** but **high-write** tables. 
      * Do not use an index if the field has **low cardinality**, the number of distinct values in that field.
        * Low-cardinality: Refers to columns with few unique values. 

## Profiling
  * [explain](https://medium.com/@sj82516/mysql-explain%E5%88%86%E6%9E%90%E8%88%87index%E8%A8%AD%E5%AE%9A%E6%9F%A5%E8%A9%A2%E5%84%AA%E5%8C%96-3e0708206ebf)
  * How to find slow query