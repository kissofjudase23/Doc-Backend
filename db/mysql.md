 # MySQL

 Table of Contents
- [MySQL](#mysql)
  - [FAQ:](#faq)
  - [Design](#design)
  - [Data Types](#data-types)
    - [Numeric Type](#numeric-type)
    - [Date and Time Type](#date-and-time-type)
    - [String Type](#string-type)
    - [JSON (from mysql 5.7)](#json-from-mysql-57)
  - [Charset](#charset)
  - [ACID Model](#acid-model)
  - [CAP](#cap)
  - [Security](#security)
  - [Normalization](#normalization)
  - [Denormalization](#denormalization)
  - [Partitions](#partitions)
  - [Sharding](#sharding)
  - [Stored Objects](#stored-objects)
    - [Stored procedure](#stored-procedure)
    - [Trigger](#trigger)
    - [Event](#event)
    - [View](#view)
  - [Indexes](#indexes)
  - [Locking and Transaction Model](#locking-and-transaction-model)
  - [Profiling](#profiling)
  - [Join](#join)
  - [Example:](#example)


## FAQ:
  * [Normalized vs. Denormalized Databases](https://medium.com/@katedoesdev/normalized-vs-denormalized-databases-210e1d67927d)
    * **Normalized databases** are designed to minimize **redundancy**, while **denomalized databases** are designed to optimized **read time**.
    * For example:
      * In a traditional normzlied database with data like Courses and Teachers, Courses might contain a column called TeachersID, which is a foreign key to Teacher.
        * One Benefit of this is that information about the teacher is **only stored once in the databases**.
        * The drawback is that many common queries will require expensive joins.
  * Partition vs Sharding
    * Partitioning is more a generic term for dividing data across tables or databases. **Sharding is one specific type of partitioning**, namely horizontal partitioning.
    * Ref:
      * https://www.quora.com/Whats-the-difference-between-sharding-DB-tables-and-partitioning-them
  * How to find slow query


## Design
  * Table
    * [Normalization](#normalization)
    * [Clustered Indexes](#clustered-indexes)
      * Unsigned big int incremental
        * Faster when insert
        * Duplicated issue
      * User defined (unique or composite fields)
    * [Secondary Indexes](#secondary-indexes)
    * [Profiling](#profiling)
    * [Partitions](#partitions)
  * APP
    * Connection Pool
      * Depend on your processes, threads in your app and capability of your mysql server.

## [Data Types](https://dev.mysql.com/doc/refman/8.0/en/data-type-overview.html)
### [Numeric Type](https://dev.mysql.com/doc/refman/8.0/en/numeric-type-overview.html)
### [Date and Time Type](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-type-overview.html)
### [String Type](https://dev.mysql.com/doc/refman/8.0/en/string-type-overview.html)
### [JSON](https://dev.mysql.com/doc/refman/8.0/en/json.html) (from mysql 5.7)
  * [How to create index on JSON](https://blog.gslin.org/archives/2016/03/09/6406/mysql-5-7-%E7%9A%84-json%E3%80%81virtual-column-%E4%BB%A5%E5%8F%8A-index/)
    * Use [Virtual Column](https://dev.mysql.com/doc/refman/8.0/en/create-table-generated-columns.html)
      * create **virtual column** on the key street
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
## ACID Model
  * Ref:
    * https://dev.mysql.com/doc/refman/8.0/en/mysql-acid.html
  * **A**tomicity
    * **Transactions** are atomic units of work that can be committed or rolled back.
    * When a transaction makes multiple changes to the database, **either all the changes succeed when the transaction is committed, or all the changes are undone when the transaction is rolled back**.
  * **C**onsistency
    * The database remains in a **consistent state** at all times.
    * If related data is being updated across multiple tables, **queries see either all old values or all new values, not a mix of old and new values**.
  * **I**solation
    * Transactions are protected (isolated) from each other while they are in progress; they cannot interfere with each other or see each other's uncommitted data.
    * [Isolation Level](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html)
      - READ UNCOMMITTED
      - READ COMMITTED
      - REPEATABLE READ
      - SERIALIZABLE
  * **D**urability
    * The results of transactions are durable: once a commit operation succeeds, the changes made by that transaction are safe from power failures, system crashes, race conditions, or other potential dangers that many non-database applications are vulnerable to.

## CAP
 * Ref:
   * https://speakerdeck.com/shlominoach/mysql-and-the-cap-theorem-relevance-and-misconceptions
* [Atomic] **C**onsistentency:
  * Once a write is successful on a node, any read on any node must reflect that write or any later write
* [High] **A**vailability:
  * Every non-crashed node must respond to requests in a finite amount of time, it is implied that response must be valid, non-error.
* **P**artition Tolerant:
  * The system is able to operate on network partitioning.
    * **Partition tolerance is considered as a given condition**, since network partitioning can and does take place regardless of a system’s design

* A distributed data store [web service] **cannot provide more than two** out of the three properties. Better illustrated as:
  * If the network is good:
    * You may achieve both **A**vailability and **C**onsisteny (AC)
  * If the network is **P**artitioned
    * You must choose between **A**valibility and **C**onsisteny (AP) or (CP)

## Security
  * [SQL Injection](https://en.wikipedia.org/wiki/SQL_injection)
      * SQL injection is a code injection technique, used to attack data-driven applications, in which malicious SQL statements are inserted into an entry field for execution.
        ```sql
        SELECT * FROM users WHERE name = '' OR '1'='1';
        ```
    * Solution:
      * **Prepare statement**, **Parameter Binding**
        * If you have any "special" characters (such as semicolons or apostrophes) in your data, they will be **automatically quoted** for you by the SQLEngine object, so you don't have to worry about quoting.
          ```python
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

## Normalization
  * Ref:
    * https://medium.com/@habibul.hasan.hira/database-normalization-8cdaddbb7715
  * Definition:
    * It is a database **design technique** which organizes tables in a manner that **reduces redundancy and dependency** of data.
    *  It divides larger tables to smaller tables and links them using relationships.
  * **1NF**
    * Every column of a table should be atomic. That means you **can not put multiple values in a database column**.
  * **2NF**
    * If we have **composite primary key**! every non key field should be fully **depended on both key**. 
  * **3NF**
    * When a database table is in second normal form, there should be no transitive functional dependency. That means **any non primary key field should not be depend on other** on primary key field.
  * **BCNF**
  * **4NF**
  * **5NF**

## Denormalization
  * Definition:
    * A database optimization technique in which we **add redundant data to one or more tables**, this can help us avoid costly joins in a relational database.
  * Pros
    * **Retrieving data is faster since we do fewer joins.**
    * Queries to retireve can be simpler, since we need to look at fewer tables.
  * Cons
    * Updates and Inserts are more expensive.
    * Making Update and Insert Code harder to write.
    * **Data may be inconsistent.**
    * **Data redundancy necessitates more storage.**


## [Partitions](https://dev.mysql.com/doc/refman/8.0/en/partitioning.html)
  * FAQ
    * [Partitions and UPDATE](https://stackoverflow.com/questions/12929624/partitions-and-update)
      * If you UPDATE a row, will the row be moved to another partition automatically, if the partition conditions of another partition is met?
      * It must move them on update. If it didn't it wouldn't work well. MySQL would have to basically scan all partitions on every query as it couldn't know where records where stored.
    * [How to drop subpartition](https://dev.mysql.com/doc/refman/5.7/en/partitioning-subpartitions.html)
    * [What is the difference between mysql drop partition and truncate partition](https://stackoverflow.com/questions/56244720/what-is-the-difference-between-mysql-drop-partition-and-truncate-partition)
      * DROP PARTITION:
        * also removes the partition from the list of partitions.
      * TRUNCATE PARTITION:
        * leaves the partition in place, but empty.

  * [Partition](http://blog.kenyang.net/2017/06/11/whats-mysql-partition)
    * Purpose:
      * **Partition Pruning**
        * Make your queries faster (select, insert, update, delete).
      * **Purge Data**
        * Drop partition is much faster.
          * **Only Range based and List based partition support drop.**
        * Truncate partition:
          * TRUNCATE PARTITION merely deletes rows; it does not alter the definition of the table itself, or of any of its partitions.
      * Note:
        * App does not aware of the partitions.
    * Type
      * Key (column)
        * Mysql would use **built-in hash function** to determine which partition the record will be stored.
        * example:
          ```sql
          CREATE TABLE t (
    	            id INT,
    	            create_time DATETIME
          )
          PARTITION BY KEY(create_time)
          PARTITIONS 10;
          ```
        * Note
          * Deleted by partition does not work
      * **Hash** (INT expression)
        * example:
          ```sql
          CREATE TABLE t (
                  id INT,
                  create_time DATETIME
          )
          PARTITION BY HASH(MONTH(create_time))
          PARTITIONS 12;
          ```
        * Note
          * Deleted by partition does not work
      * **List** (INT expression)
        * example:
          ```sql
          CREATE TABLE t (
                  id INT,
                  create_time DATETIME
          )
          PARTITION BY LIST(DAYOFWEEK(create_time)) (
            PARTITION pMon VALUES IN (1),
            PARTITION pTue VALUES IN (2),
            PARTITION pWed VALUES IN (3),
            PARTITION pThu VALUES IN (4)
          );
          ```
      * **Range** (INT expression)
        * example:
          ```sql
          CREATE TABLE t (
                  id INT,
                  create_time DATETIME
          )
          PARTITION BY RANGE(YEAR(create_time)) (
            PARTITION p2017 VALUES LESS THAN (2018),
            PARTITION p2018 VALUES LESS THAN (2019),
            PARTITION p2019 VALUES LESS THAN (2020)
          );
          ```

  * [Subpartition](https://dev.mysql.com/doc/refman/5.7/en/partitioning-subpartitions.html)

## [Sharding](https://medium.com/system-design-blog/database-sharding-69f3f4bd96db)
  * FAQ:
    * [Differences Between the NDB and InnoDB Storage Engines](https://dev.mysql.com/doc/refman/5.7/en/mysql-cluster-ndb-innodb-engines.html)
  * Approach1: calculate shard key in the app layers
  * Approach2: use [mysql cluster](https://dev.mysql.com/doc/refman/8.0/en/mysql-cluster-overview.html)
    * ![overview](https://dev.mysql.com/doc/refman/8.0/en/images/cluster-components-1.png)
  * Advantages:
    * Faster queries response.
    * More write bandwidth
    * Scaling out
  * Drawbacks:
    * Adds complexity in the system.
    * Rebalancing data.
    * Joining data from multiple shards.
  * Sharding Architecture:
    * Hash Based
    * Range Based
    * Directory Based

## [Stored Objects](https://dev.mysql.com/doc/refman/8.0/en/stored-objects.html)
### Stored procedure
  * An object created with CREATE FUNCTION and used much **like a built-in function**. You invoke it in an expression and it returns a value during expression evaluation.
### Trigger
  * An object created with CREATE TRIGGER that is associated with a table. **A trigger is activated when a particular event occurs for the table**, such as an insert or update.
### Event
  * An object created with CREATE EVENT and invoked by the server according to **schedule**.
### View
  * An object created with CREATE VIEW that when referenced produces a result set. A view acts as a virtual table.

## Indexes
  * Ref:
    * https://medium.com/@User3141592/single-vs-composite-indexes-in-relational-databases-58d0eb045cbe

  * Good Practices:
    * Use good indexes
      * Columns that you are querying (**SELECT, GROUP BY, ORDER BY, JOIN**) could be faster with indices.
      * Indices are usually represented as **self-balancing B-tree** that keeps data sorted and allows searches, sequential access, insertions, and deletions in logarithmic time.
      * Writes could also be slower since the index also needs to be updated.
    * Use good **composite indexes**:
      * If certain fields tend to **appear together in queries**, then it’s a good idea to create a composite index on them.
      * Similar to single indexes, the cardinality of the fields matters to the effectiveness composite indexes.
      * if a composite index is made on column1 , column2 , column3 ,…, columnN . Then queries like
        ```sql
        SELECT * FROM table
        WHERE column1 = 'value';

        SELECT * FROM table
        WHERE column1 = 'value1'
        AND column2 = 'value2';

        SELECT * FROM table
        WHERE column1 = 'value1'
        AND column2 = 'value2'
        AND column3 = 'value3'

        ...

        SELECT * FROM table
        WHERE column1 = 'value1'
        AND column2 = 'value2'
        AND column3 = 'value3'
          ...
        AND columnN = 'valueN'

        ```
        will have performance gain.
    * Avoid Unnecessary Indexes
      * Do not use an index for **low-read** but **high-write** tables.
      * Do not use an index if the field has **low cardinality**.
        * Low-cardinality: refers to columns with few unique values.
        * [Why low cardinality indexes negatively impact performance](https://www.ibm.com/developerworks/data/library/techarticle/dm-1309cardinal/index.html])

  * Clustered Indexes vsSecondary Indexes (InnoDB)
    * Ref:
      * https://medium.com/@genchilu/%E6%B7%BA%E8%AB%87-innodb-%E7%9A%84-cluster-index-%E5%92%8C-secondary-index-f75da308352e

    * Clustered Indexes
      * Every InnoDB table has a special index called the clustered index where the data for the rows is stored. **Typically, the clustered index is synonymous with the primary key**.
        * When you define a PRIMARY KEY on your table, InnoDB uses it as the clustered index.
        * If you do not define a PRIMARY KEY for your table, MySQL locates the first UNIQUE index where all the key columns are NOT NULL andInnoDB uses it as the clustered index.
        * If the table has no PRIMARY KEY or suitable UNIQUE index, InnoDB internally generates a hidden clustered index named GEN_CLUST_INDEX on a synthetic column containing row ID values.
      * Data Structure
        * **B-Tree**
            * The data in a Node is in the same page (physical unit).
            * Non-Leaf Nodes
               *  **Clustered Keys** and **Pointers to the Child Nodes**
            * Leaf Nodes
               *  **Clustered Keys**, **Raw Data** and **Pointers to the Sibling Nodes**
            * ![cluster_indexes](images/cluster_indexes.png)
      * Data Coverage:
        * All, since list of leaf nodes is the table.
      * Condition Coverage:
        * Depended on the clustered key, please refer [rule of composite indexes](https://medium.com/@User3141592/single-vs-composite-indexes-in-relational-databases-58d0eb045cbe) (The condition order is important)

    * Secondary Indexes
      * All indexes other than the clustered index are known as secondary indexes.
      * In InnoDB, each record in a secondary index contains the **primary key columns** for the row, as well as the **columns specified for the secondary index**. InnoDB uses this primary key value to search for the row in the clustered index.

      * Data Structure
        * B-Tree
          * The data in a Node is in the same page (physical unit).
          * Non-Leaf Nodes
             *  **Secondary Keys** and **Pointers to the Child Nodes**
          * Leaf Nodes
             *  **Secondary Keys** , **Clustered Keys** and and **Pointers to the Sibling Nodes**
             *  The List of leaf nodes is the **sorted clustered keys**
             *  **If the primary key is long, the secondary indexes use more space**, so it is advantageous to have a short primary key.
          * ![secondary_indexes](images/secondary_indexes.png)
            * **The data structure in the bottom is clustered indexes (the table).**
            * **Need another lookup to get raw data in the clustered index if the secondary key and clustered key can not cover the wanted columns**, that is, explain will show "Using Index Condition".
            * [Using Index vs Using Index Condition](https://stackoverflow.com/questions/1687548/mysql-explain-using-index-vs-using-index-condition)
      * Data Coverage:
        * Fields in **secondary key and clustered key** (permutation)
      * Condition Coverage:
        * Depended on the secondary key, please refer [rule of composite indexes](https://medium.com/@User3141592/single-vs-composite-indexes-in-relational-databases-58d0eb045cbe) (The condition order is important)


## Locking and Transaction Model
  * Ref:
    * https://dev.mysql.com/doc/refman/8.0/en/innodb-locking-transaction-model.html
    * https://dev.mysql.com/doc/refman/8.0/en/locking-issues.html



## Profiling
  * [explain](https://medium.com/@sj82516/mysql-explain%E5%88%86%E6%9E%90%E8%88%87index%E8%A8%AD%E5%AE%9A%E6%9F%A5%E8%A9%A2%E5%84%AA%E5%8C%96-3e0708206ebf)
  * How to find slow query


## Join
  * Ref
    * https://katiekodes.com/sql-every-join/

  * Cross Join
    * CROSS JOIN returns the **Cartesian product of rows** from tables in the join. In other words, it will produce rows which combine each row from the first table with each row from the second table

  * (Inner) Join
    * The result set would contain **only the data where the criteria match**.

  * Left (outer) Join
    * The result set will **contain all records from the left table**.
    * If **no matching recrods were found in the right table**, then **its fields will contain the NULL vales**.

  * Right (outer) Join
    * The opposite of Left JOIN.
    * A LEFT JOIN B is equivalent to B RIGHT JOIN A

  * FULL (outer)
    * The type of join **combines the results of LEFT and RIGHT JOINS**. All records from both tables will be included in the result set. If no matching record was found, then the corresponding result fields will have a NULL value.

  * Self Join
  * Left Anti Join
  * Right Anti Join

## Example:
  * Tables
    * Courses:
      * CourseID*
      * CourseName
      * TeacherID
    * Teachers:
      * TeacherID*
      * TeacherName
    * Students:
      * StudentID*
      * StudentName
    * StudentCourses:
      * CourseID
      * StudentID

  * Questions
    * Implement a query to get a list of all students and how many course each student is enrolled in.
      * **We can only select values that are in aggregate function or in the GROUP BY clause.**
      * Approach1: group by multiple keys
        ```sql
        SELECT StudentID, StudentName, count(StudentCourses.CourseID) as [Cnt]
        FROM (Students LEFT JOIN StudentCourses ON Students.StudentID = StudentCourses.StudentID)
        GROUP BY Students.StudentID, Students.StudentName;
        ```
      * Approach2: Use 2 aggregated functions
        ```sql
        SELECT StudentID, max(Students.StudentName), count(StudentCourses.CourseID) as [Cnt]
        FROM (Students LEFT JOIN StudentCourses ON Students.StudentID = StudentCourses.StudentID)
        GROUP BY Students.StudentID;
        ```
    * Implement a query to get a list of all teachers and how many students thery each teach.
      * Get a list of TeacherID and how many students are associated with each TeacherID
        * count(StudentCourses.CourseID) is the student cnt for each teacher
          * Allow duplicated students (If a teacher teaches the same student in two courses, the count will be double) .
        ```sql
        SELECT TeacherID, count(Courses.CourseID) as [Number]
        FROM (Courses INNER JOIN StudentCourses on Courses.CourseID = StudentCourses.CourseID)
        GROUP BY Courses.TeacherID;
        ```
      * select the teachers who aren't teaching class
        ```sql
        SELECT TeacherName, isnull(StudentSize.Number, 0)
        FROM Teachers LEFT JOIN
          (SELECT TeacherID, count(Courses.CourseID) as [Number]
           FROM (Courses INNER JOIN StudentCourses on Courses.CourseID == StudentCourses.CourseID)
           GROUP BY Courses.TeacherID) StudentSize
        on Teachers.TeacherID = StudentSize.TeacherID
        ORDER BY StudentSize.Number DSEC;
        ```
    * [Find the second largetst value](https://stackoverflow.com/questions/32100/what-is-the-simplest-sql-query-to-find-the-second-largest-value)
      ```sql
      SELECT MAX( col )
      FROM table
      WHERE col < ( SELECT MAX( col )
                    FROM table )
      ```
  * [Query to find nth max value of a column](https://stackoverflow.com/questions/80706/query-to-find-nth-max-value-of-a-column)
    * on MySQL you can do
      ```sql
      SELECT column FROM table ORDER BY column DESC LIMIT 7,10;
      ```
    * Updated as per comment request. WARNING completely untested!
      ```sql
      SELECT DOB FROM (SELECT DOB FROM USERS ORDER BY DOB DESC) WHERE ROWID = 6
      ```
  * [Filter Table Before Applying Left Join](https://stackoverflow.com/questions/15077053/filter-table-before-applying-left-join)
    * Solution1:
      ```sql
      SELECT c.Customer, c.State, e.Entry
      FROM Customer c LEFT JOIN Entry e
        ON c.Customer=e.Customer
        AND e.Category='D'
      ``
  * Solution2:
      ```sql
      SELECT c.Customer, c.State, e.Entry
      FROM Customer AS c LEFT JOIN (SELECT * FROM Entry WHERE Category='D') AS e ON c.Customer=e.Customer
      ```







