# Notes [![Awesome](https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg)](https://github.com/sindresorhus/awesome)

## Table of Contents
 - [Database](#Database)
   - [MySQL](#MySQL)
 - [Caching](#Caching)
   - [Redis](#Redis)
 - [ComputerNetwork](#ComputerNetwork)
 - [WebServices](#WebServices)
 - [Reference](#Reference)
 

## Database
  ### MySQL  
  * [Data Types](https://dev.mysql.com/doc/refman/8.0/en/data-type-overview.html)
      * [Numeric Type](https://dev.mysql.com/doc/refman/8.0/en/numeric-type-overview.html)
      * [Date and Time Type](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-type-overview.html)
      * [String Type](https://dev.mysql.com/doc/refman/8.0/en/string-type-overview.html)
  * [ACID Model](https://dev.mysql.com/doc/refman/8.0/en/mysql-acid.html)（InnoDB Engine)
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
    * [Security]
      * SQL Injection
    * [Normalization](https://www.mysql.tw/2013/03/normalization.html)
    * [Partitions](https://dev.mysql.com/doc/refman/8.0/en/partitioning.html)
    * [Stored Objects](https://dev.mysql.com/doc/refman/8.0/en/stored-objects.html)
      * Stored procedure
      * Trigger
      * Event
      * View
    * [Optimization](https://dev.mysql.com/doc/refman/8.0/en/optimization.html)
    * Profiling
      * [explain](https://medium.com/@sj82516/mysql-explain%E5%88%86%E6%9E%90%E8%88%87index%E8%A8%AD%E5%AE%9A%E6%9F%A5%E8%A9%A2%E5%84%AA%E5%8C%96-3e0708206ebf)
    
  * MongoDB
    * [ACID](https://www.mongodb.com/transactions) (MongoDB 4.0)
    * [CAP](https://stackoverflow.com/questions/11292215/where-does-mongodb-stand-in-the-cap-theorem)
   
## Caching  
  ### [Memcached vs. Redis](https://stackoverflow.com/questions/10558465/memcached-vs-redis)
  * Redis is more powerful, more popular, and better supported than memcached. Memcached can only do a small fraction of the things Redis can do. Redis is better even where their features overlap. For anything new, use Redis
  ### [Redis](https://redis.io/)
  * Document
  * [Persistence](https://redis.io/topics/persistence)
    * By default redis persists your data to disk using a mechanism called snapshotting.
  * [Eviction Policies](https://redis.io/topics/lru-cache)
    - noneviction
    - allkeys-lru
    - volatile-lru
    - allkeys-random
    - volatile-random
    - volatile-ttl 
  * [Data Types](https://redis.io/topics/data-types-intro)
    * Strings
    * Hashed
    * Lists
    * Sets
    * Sorted Sets
    * Geo
    * Bitmap
    * HyperLoglog
  * Transactions and Atomicity
  * [Lua Scripting](https://redis.io/commands/eval)
    * You can kind of think of lua scripts like redis's own SQL or stored procedures. It's both more and less than that, but the analogy mostly works.
  * [Pipelining](https://redis.io/topics/pipelining)
    * If you have many redis commands you want to execute you can use pipelining to send them to redis all-at-once instead of one-at-a-time.
    * ```python
      import redis
      r = redis.Redis(host='localhost', port=6379, db=0)

      # Create a pipeline instance 
      pipe = r.pipeline()

      # Send the commands to be executed in batch
      pipe.set("key1", "value1")
      pipe.get("key2")
      pipe.hgetall("key3")
      pipe.set("key4","value4")

      # execute all the buffered commands
      responses = pipe.execute()

      # get the results
      for response in responses:
          print(response)
      ```
  * Design Patterns
    * [Reliable queue](https://redis.io/commands/rpoplpush)
    * [Circular list](https://redis.io/commands/rpoplpush)
    * [Pub/Sub](https://redis.io/topics/pubsub)
      * [example](https://github.com/andymccurdy/redis-py/#publish--subscribe)
      * [Only one client can get the message.](https://stackoverflow.com/questions/7196306/competing-consumer-on-redis-pub-sub-supported) (not one to many)
    * [Distributed lock](https://redis.io/topics/distlock) (Redlock algorithm)
      * [python implementation](https://github.com/SPSCommerce/redlock-py)
      
      
## ComputerNetwork
  * [OSI Model](https://en.wikipedia.org/wiki/OSI_model)
  * [TCP vs UDP](https://stackoverflow.com/questions/5970383/difference-between-tcp-and-udp)
  * [TCP three way handshake](https://notfalse.net/7/three-way-handshake)
## WebServices
  * Auth
    - Authentication vs Authorization
      - Authentication is the process of ascertaining that somebody really is who they claim to be.
      - Authorization refers to rules that determine who is allowed to do what. E.g. Adam may be authorized to create and delete databases, while Usama is only authorised to read.
    * TLS handshake
    * JWT
    * OAuth2.0
  * HTTP1.1 vs HTTP2.0
  * Design Patterns
    * RestAPI
  * [Forward vs Redirect](https://stackoverflow.com/questions/6068891/difference-between-jsp-forward-and-redirect)
    * Forward
      - redirect sets the response status to 302, and the new url in a Location header, and sends the response to the browser. Then the browser, according to the http specification, makes another request to the new url
    * Redirect
      - forward happens entirely on the server. The servlet container just forwards the same request to the target url, without the browser knowing about that. Hence you can use the same request attributes and the same request parameters when handling the new url. And the browser won't know the url has changed (because it has happened entirely on the server)
  * MicroServices
    * Advantages:
    * Drawbacks:
      * Performance
      * Lambda Limits
        * Account Limits (e.g. aws lambda instance count)
        * Cold Start Time
  * Profiling
    * wrk 
## Reference 
  * [awsesome-interview-questions](https://github.com/MaximAbramchuck/awesome-interview-questions)
