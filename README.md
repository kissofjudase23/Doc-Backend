# Notes [![Awesome](https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg)](https://github.com/sindresorhus/awesome)

## Table of Contents
 - [Database](#Database)
 - [Caching](#Caching)
 - [WebServices](#WebServices)
 - [Reference](#Reference)
 

## Database
## Caching  
  * [Memcached vs. Redis](https://stackoverflow.com/questions/10558465/memcached-vs-redis)
    * Redis is more powerful, more popular, and better supported than memcached. Memcached can only do a small fraction of the things Redis can do. Redis is better even where their features overlap. For anything new, use Redis
  * [Redis](https://redis.io/)
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
      - [Reliable queue](https://redis.io/commands/rpoplpush)
      - [Circular list](https://redis.io/commands/rpoplpush)
      - [Pub/Sub](https://redis.io/topics/pubsub)
        - [example](https://github.com/andymccurdy/redis-py/#publish--subscribe)
        - [Only one client can get the message.](https://stackoverflow.com/questions/7196306/competing-consumer-on-redis-pub-sub-supported) (not one to many)
      - [Distributed lock](https://redis.io/topics/distlock) (Redlock algorithm)
        - [python implementation](https://github.com/SPSCommerce/redlock-py)
      
      
## WebServices
## Reference 
  - [awsesome-interview-questions](https://github.com/MaximAbramchuck/awesome-interview-questions)
