
## Table of Contents
- [FAQ](#faq)
  - [Memcached vs. Redis](#memcached-vs-redis)
- [Reference](#reference)
- [Persistence](#persistence)
- [Eviction Policies](#eviction-policies)
- [Data Types](#data-types)
  - [Strings](#strings)
  - [Hashed](#hashed)
  - [Lists](#lists)
  - [Sets](#sets)
  - [Sorted Sets](#sorted-sets)
  - [Geo](#geo)
  - [Bitmap](#bitmap)
  - [HyperLoglog](#hyperloglog)
- [Transactions](#transactions)
- [Lua Scripting](#lua-scripting)
- [Performance](#performance)
  - [Mget](#mget)
  - [Pipelining](#pipelining)
- [Design Patterns](#design-patterns)
  - [Reliable queue](#reliable-queue)
  - [Pub/Sub](#pubsub)
  - [Distributed lock (Redlock algorithm)](#distributed-lock-redlock-algorithm)


## FAQ
### [Memcached vs. Redis](https://stackoverflow.com/questions/10558465/memcached-vs-redis)
  - Redis is more powerful, more popular, and better supported than memcached. Memcached can only do a small fraction of the things Redis can do. Redis is better even where their features overlap. For anything new, use Redis

## Reference
  * [Redis.io](https://redis.io/)
  * [Try.Redis.io](try.redis.io)
  * Redis-py
    * [sample code](https://github.com/andymccurdy/redis-py)
    * [document](https://redis-py.readthedocs.io/en/latest/)
 
  
##  [Persistence](https://redis.io/topics/persistence)
  * By default redis persists your data to disk using a mechanism called **snapshotting**.
   
## [Eviction Policies](https://redis.io/topics/lru-cache)
  * noneviction
  * allkeys-lru
  * volatile-lru
  * allkeys-random
  * volatile-random
  * volatile-ttl 
   
## [Data Types](https://redis.io/topics/data-types-intro)
### Strings
### Hashed
### Lists
### Sets
### Sorted Sets
### Geo
### Bitmap
### HyperLoglog
 

## [Transactions](https://redis.io/topics/transactions)
  * MULTI, EXEC, DISCARD and WATCH are the foundation of transactions in Redis. They allow the execution of a group of commands in a single step, with two important guarantees:
  * Atomicity
    * Either **all of the commands or none are processed**, so a Redis transaction is also atomic.
  * Isolation
    * All the commands in a transaction are serialized and executed sequentially.
    * It can never happen that a request issued by another client is served in the middle of the execution of a Redis transaction. **This guarantees that the commands are executed as a single isolated operation**.
  * **Does not support roll back**
    * Redis commands can fail during a transaction, **but still Redis will execute the rest of the transaction instead of rolling back**.
* [What are equivalent functions of MULTI and EXEC commands in redis-py?](https://stackoverflow.com/questions/31769163/what-are-equivalent-functions-of-multi-and-exec-commands-in-redis-py)
  * In redis-py MULTI and EXEC can only be used through a [Pipeline](https://redis-py.readthedocs.io/en/latest/index.html?redis.Redis.pipeline#redis.Redis.pipeline) object.
    * ```python
      r = redis.Redis(transaction=True)
      p = r.pipeline()
      p.set("transError", var)
      p.execute()
      ```
    * With the monitor command through the redis-cli you can see MULTI, SET, EXEC sent when p.execute() is called. To omit the MULTI/EXEC pair, use r.pipeline(transaction=False).


##  [Lua Scripting](https://redis.io/commands/eval)
  * You can kind of think of lua scripts like redis's own SQL or stored procedures. It's both more and less than that, but the analogy mostly works.
  

## Performance
### [Mget](https://redis.io/commands/mget)
  * Returns the values of **all specified keys**
### [Pipelining](https://redis.io/topics/pipelining)
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

## Design Patterns
### [Reliable queue](https://redis.io/commands/rpoplpush)
  * [Circular list](https://redis.io/commands/rpoplpush)
### [Pub/Sub](https://redis.io/topics/pubsub)
  * [example](https://github.com/andymccurdy/redis-py/#publish--subscribe)
  * [Only one client can get the message.](https://stackoverflow.com/questions/7196306/competing-consumer-on-redis-pub-sub-supported) (not one to many)
### [Distributed lock](https://redis.io/topics/distlock) (Redlock algorithm)
  * [python implementation](https://github.com/SPSCommerce/redlock-py)