###### tags: `redis`

# Redis

## Table of Contents
- [Table of Contents](#table-of-contents)
- [Reference](#reference)
- [FAQ](#faq)
- [Performance enhancement](#performance-enhancement)
- [Info](#info)
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


## Reference
  * [Redis.io](https://redis.io/)
  * [Try.Redis.io](https://try.redis.io/)
  * Redis-py
    * [sample code](https://github.com/andymccurdy/redis-py)
    * [document](https://redis-py.readthedocs.io/en/latest/)


## FAQ
 * [Memcached vs. Redis](https://stackoverflow.com/questions/10558465/memcached-vs-redis)
   * Redis is more powerful, more popular, and better supported than memcached. Memcached can only do a small fraction of the things Redis can do. Redis is better even where their features overlap. For anything new, use Redis
 * How to evaluate cache hit rate?
   * Overall:
     * You can see **keyspace_hits** and **keyspace_misses** in redis [info]((https://redis.io/commands/info))
   * Specific key:
     * Write log and do some post process.
 * [Pipelining vs transaction in redis](https://stackoverflow.com/questions/29327544/pipelining-vs-transaction-in-redis)
   * Pipelining is primarily a network optimization. It essentially means the client buffers up a bunch of commands and ships them to the server in one go. The commands are not guaranteed to be executed in a transaction. The benefit here is saving network round trip time for every command.
   * Redis is single threaded so an individual command is always atomic, but two given commands from different clients can execute in sequence, alternating between them for example.
   * Multi/exec, however, ensures no other clients are executing commands in between the commands in the multi/exec sequence

## Performance enhancement
   * Reduce TTL time
     * [Mget](https://redis.io/commands/mget)
        * Returns the values of **all specified keys**
     * [Pipelining](https://redis.io/topics/pipelining)
       * If you have many redis commands you want to execute you can use pipelining to send them to redis all-at-once instead of one-at-a-time.
       * Only one round trip time
       * Example:

          ```python
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
## [Info](https://redis.io/commands/info)
  * Ref:
    * [AWS ElastiCache Metrics](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/CacheMetrics.Redis.html)
  * [Redis Info](https://redis.io/commands/info)
    * The INFO command returns information and statistics about the server in a format that is simple to parse by computers and easy to read by humans.


## [Persistence](https://redis.io/topics/persistence)
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
### [HyperLoglog](https://thoughtbot.com/blog/hyperloglogs-in-redis)
  * A HyperLogLog is a probabilistic data structure used to count unique values
    * or as it’s referred to in mathematics: calculating the cardinality of a set.
    * These values can be anything: for example, IP addresses for the visitors of a website, search terms, or email addresses.
  * **Counting unique values with exact precision requires an amount of memory proportional to the number of unique values.**
    * The reason for this is that there is no way of determining if a value has already been seen other than by comparing it to the previously seen values.
  * A HyperLogLog solves this problem by allowing to trade memory consumption for precision **making it possible to estimate cardinalities larger than 10^9 with a standard error of 2% using only 1.5 kilobytes of memory**.


## [Transactions](https://redis.io/topics/transactions)
  * They allow the execution of a group of commands in a single step, with two important guarantees:
    * **Atomicity**
      * **Either all of the commands or none are processed**.
    * **Isolation**
      * All the commands in a transaction are serialized and executed sequentially.
      * **It can never happen that a request issued by another client is served in the middle of the execution** of a Redis transaction.
    * **Does not support roll back**
      * Redis commands can fail during a transaction, **but still Redis will execute the rest of the transaction instead of rolling back**.

  * **MULTI**, **EXEC**, **DISCARD** and **WATCH** are the foundation of transactions in Redis.
    * MULTI and EXEC
        ```redis
        MULTI

        INCR foo
        INCR bar
        ...

        EXEC
        ```
    * DISCARD
      * Used to abort a transaction.
    * WATCH
      * It is a command that will **make the EXEC conditional**: **we are asking Redis to perform the transaction only if none of the WATCHed keys were modified.**
        * if there are race conditions and another client modifies the result of val in the time between our call to WATCH and our call to EXEC, the transaction will fail.
        ```redis
        WATCH mykey
        val = GET mykey
        val = val + 1

        MULTI
        SET mykey $val
        EXEC
        ```
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


## Design Patterns
### [Reliable queue](https://redis.io/commands/rpoplpush)
  * [Circular list](https://redis.io/commands/rpoplpush)
### [Pub/Sub](https://redis.io/topics/pubsub)
  * example
    * [redis-py](https://github.com/andymccurdy/redis-py/#publish--subscribe)
      * Subscribe channel
         ```python
         r = redis.Redis(...)
         p = r.pubsub()

         # subscribe by channel
         p.subscribe('my-first-channel', 'my-second-channel', ...)

         # subscribe by pattern
         p.psubscribe('my-*', ...)
         ```
      * Publish message
          ```python
          # delivery_cnt would be 2
          # my-firs-channel matches 'my-first-channel' and 'my-*'

          delivery_cnt =  r.publish('my-first-channel', 'some data')
          ```
      * note:
        * [p]unsubscribe doesn't remove channels/patterns from the dictionary because messages on those channels could still be in flight from the server to the client when the unsubscribe command is sent. Instead retrieving messages from a pubsub object (either via pubsub.listen() or pubsub.get_message()) will find the [p]unsubscribe confirmations and only then remove the channels/patterns. This is the only safe time to remove them.
        * **Alternatively, consider using pubsub.close()**. That will shut down everything, including disconnecting and cleaning out the channels/patterns dicts.

Alternatively, consider using pubsub.close(). That will shut down everything, including disconnecting and cleaning out the channels/patterns dicts.
  * [Only one client can get the message](https://stackoverflow.com/questions/7196306/competing-consumer-on-redis-pub-sub-supported) (not one to many).
    * But you can publish to multiple channel once.

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

	* Note:
  	* Need at least 3 machines (vote).

  * Implementation
    * [Redlock-py](https://github.com/SPSCommerce/redlock-py)
      * Python Implementation
    * [aioredlock](https://github.com/joanvila/aioredlock)
      * Asyncio Python Implementation, for python3.5+
    * [Redsync.go](https://github.com/hjr265/redsync.go)
      * Go implementation