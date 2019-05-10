# Interviews [![Awesome](https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg)](https://github.com/sindresorhus/awesome)


## Table of Contents
 - [Programming Languages]
 - [Database](#Database)
 - [Caching](#Caching)
 - [WebServices](#WebServices)
 

## Database
## Caching  
  * [Memcached vs. Redis](https://stackoverflow.com/questions/10558465/memcached-vs-redis)
    * Redis is more powerful, more popular, and better supported than memcached. Memcached can only do a small fraction of the things Redis can do. Redis is better even where their features overlap. For anything new, use Redis
  * The Redis Suportset
    * Document
    * Many Data Types
      * Strings
      * Hashed
      * Lists
      * Sets
      * Sorted Sets
      * Geo
      * Bitmap
      * HyperLoglog
    * Transactions and Atomicity
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

## WebServices
