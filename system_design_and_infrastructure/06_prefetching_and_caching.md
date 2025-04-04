<div align='center'>
  <h1> Prefetching and Caching </h1>
</div>

# Table of Contents

- Prefetching
- Caching
  - Expiration policy
  - The cold-start problem in massive-scale systems
  - Eviction Cache Policies
  - Caching Technologies

# Prefetching

- Prefetching involves proactively loading data into the cache before it is needed, keeping it ready for quick access in order to minimize latency and improve performance.
- Caching, on the other hand, typically involves adding or removing data according to a cache policy.

# Caching

Caching strategies are used to avoid disk seeks as much as possible to enhance performance, i.e., to reduce request-response latency by storing frequently accessed data in RAM enabling the system to retrieve a response in milliseconds.

The internal caching in a database is not enough, and one needs a caching layer whose job is to keep in-memory copies of the most popular requests. This caching layer can be built into the application server (generally built-in off-the-shelf) or be a fleet of servers that are independent of the application.

Every cache server is responsible for a segment of the database that was distributed across the application server. Make sure to have a cache closer to the application hosts to avoid a hop to the data center.

## Expiration Policy

Defines how long data is cached. Too long and your data goes stale. Too short and the cache won't work.

## The cold-start problem in massive-scale systems

Is a problem that arises when the caching layer goes down and one needs to restart it. When this happens, all the traffic is going to hit the database until the caching layer gets prime. This can cause the database server to crash. One way of solving this is to generate artificial traffic to the caching layer by playing back logs from previous data.

## Eviction Cache Policies

Eviction cache policies are used to determine how data is added or removed from a cache layer.

- Least Recently Used (LRU): most used keys are moved to the head of a doubled linked list while least recently used are moved to the tail and evicted when space is needed. It evicts data that hasn't been accessed in the longest amount of time. Works at scale assuming a large enough cache.

- Least Frequently Used (LFU): evict keys that are less frequently used. Used for small caches.

- First In First Out (FIFO): the first added item in the cache is the first to be out.

- Last In First Out (LIFO): the last added item in the cache is the first to be out.

- Most Recently Used (MRU): the opposite of LRU, i.e., the most recent item is discarded first.

- Random Replacement (RR): randomly discards an item.

## Caching Technologies

- Memcache: is an open-source in-memory key-value database. 

- Redis: is a complex in-memory key-value database that supports advanced data structures.

- AWS ElastiCache: is a built-in-memory key-value store from AWS.

- Ncache: is an in-memory distributed caching solution for .NET and Java applications.

- Ehcache: is an open-source, Java-based cache that supports distributed caching.
