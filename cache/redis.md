
## Table of Contents
- [FAQ](#faq)
  - [Memcached vs. Redis](#memcached-vs-redis)
  - [Memory Metrics](#memory-metrics)
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
### Memory Metrics
  * [memory usage](https://redis.io/commands/memory-usage)
  * evicted keys
    * Number of keys removed due to reaching the maxmemory limit

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
  * Distributed locks are a very useful primitive in many environments where different processes must operate with shared resources in a mutually exclusive way.
* Safety and Liveness guarantees:
    * Safetey property
      * **Mutual exclusion**. At any given moment, only one client can hold a lock.
    * Liveness property A
      * **Deadlock free**. Eventually it is always possible to acquire a lock, even if the client that locked a resource crashes or gets partitioned.
    * Liveness property B
      * **Fault tolerance**. As long as the majority of Redis nodes are up, clients are able to acquire and release locks.
  * Why failover-based implementation are not enough
    * The simplest way to use Redis to lock a resource is to create a key in an instance. The key is usually created with a limited time to live, using the Redis expires feature, so that eventually it will get released (property 2 in our list). When the client needs to release the resource, it deletes the key.
    * There is an obvious race condition with this model:
      * Client A acquires the lock in the master.
      * The master crashes before the write to the key is transmitted to the slave.
      * The slave gets promoted to master.
      * Client B acquires the lock to the same resource A already holds a lock for. **SAFETY VIOLATION!**
  * The **Redlock** algorithm
    * In the distributed version of the algorithm we assume we have N Redis masters.
    * In order to acquire the lock, the client performs the following operations:
  		1. It gets the current time in milliseconds.
  		2. It tries to **acquire the lock in all the N instances** sequentially, using the same key name and random value in all the instances.
     		* During step 2, when setting the lock in each instance, the client uses a timeout which is small compared to the total lock auto-release time in order to acquire it.
     		* For example if the auto-release time is 10 seconds, the timeout could be in the ~ 5-50 milliseconds range. This prevents the client from remaining blocked for a long time trying to talk with a Redis node which is down: if an instance is not available, we should try to talk with the next instance ASAP.
  		3. The client computes how much time elapsed in order to acquire the lock, by subtracting from the current time the timestamp obtained in step 1. If and only if the client was able to acquire the lock in the majority of the instances (at least 3), and the total time elapsed to acquire the lock is less than lock validity time, the lock is considered to be acquired.
  		4. If the lock was acquired, its validity time is considered to be the initial validity time minus the time elapsed, as computed in step 3.
  		5. If the client failed to acquire the lock for some reason (either it was not able to lock **N/2+1** instances or the validity time is negative), it will try to unlock all the instances (even the instances it believed it was not able to lock).
	* Sample Code:
    	* [redlock](https://github.com/SPSCommerce/redlock-py/blob/master/redlock/__init__.py)
    	* [test_redlock](https://github.com/SPSCommerce/redlock-py/blob/master/tests/test_redlock.py)

  * Implementation
    * [Redlock-py](https://github.com/SPSCommerce/redlock-py)
      * Python Implementation
    * [aioredlock](https://github.com/joanvila/aioredlock)
      * Asyncio Python Implementation, for python3.5+
    * [Redsync.go](https://github.com/hjr265/redsync.go)
      * Go implementation