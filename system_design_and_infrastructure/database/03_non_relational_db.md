<div align='center'>
  <h1>Non-Relational (NoSQL) Databases</h1>
</div>

# Table of Contents

- [About](#About)
- [Pros and Cons](#pros-and-cons)
- [Data formats](#data-formats)
- [Paradigms](#Paradigms) 
  - Key-Value Pairs
  - Wide Columns
  - Document Stores
  - Graph Stores
  - In-memory
- [Best practices](#best-practices)
- [Queries](#Queries)
- [ACID or BASE-compliant](#ACID-or-BASE-compliant)
- [Key Takeaways](#Key-Takeaways)  

# About

Non-relational databases are useful for storing non-relational unstructured data, i.e., with a flexible schema (that can be changed on the fly). Non-relational databases can either prioritize `Availability` and `Partition-Tolerance`  (e.g., Cassandra), or `Consistency` and `Partition-Tolerance` (e.g., DynamoDB). NoSQL Databases **do not support SQL-style JOIN operations** by design, since JOINs can render performance bottlenecks at scale when requiring many round trips during query execution. NoSQL can be used together with Relational databases through Apache Hive or Redshift.

# Pros and Cons

- Pros: suitable for OLTP (transactional) workloads, and dynamic data. Writing is fast. It is easier and cheaper to scale horizontally since it **avoids table JOINs**. Since NoSQL databases use simple data formats (JSON, XML), they are well suitable for applications of low-latency (high speed) requirements that have:
  - High volume: a large amount of data.
  - High velocity: large number of I/O operations (concurrent connections) per second.
  - High variety: semi-structured or unstructured data.
    
- Cons: data might look different without a schema.

# Data formats

There are two types of data formats commonly stored in NoSQL databases:

- Semi-structured data:
  - Description: data does not conform to a fixed schema like traditional relational databases. Instead, it contains tags or markers to separate semantic elements and enforce hierarchies. Examples include JSON, XML, and HTML formats.
  - Use cases: data that can have a variable schema, such as spreadsheet data, weather data, and surveys.
  - Technologies for storage: `Apache Casandra`, `MongoDB`, `DynamoDB`, `Apache HBase`, and data lakes such as `AWS S3`.
  
- Unstructured data:
  - Description: data does not have a fixed data structure. Used to store images, sounds, videos, and text documents.
  - Use cases: storage of photos, movies, music, books, emails, chat messages, social media posts, and web pages.
  - Technologies for storage: data lakes such as `AWS S3`.

# Paradigms

Non-relational data can be stored in 5 different ways:

- [Key-Value Database](https://aws.amazon.com/nosql/key-value/): data is stored in key-value pairs. Ideal for scenarios where fast access (read-heavy operations) to data is crucial.
  - Technologies: `DynamoDB`, `CouchBase`, `Redis`, and `Memcached`.
  - Use cases: for session management, e-commerce main database, social media storage (user, reactions, comments, photos, etc), caching layer to reduce data latency, etc.

- [Wide-column Database](https://www.scylladb.com/glossary/wide-column-database/): optimized for write-heavy workloads of columns, but low-read. Easy to scale and replicate data across nodes. 
  - Technologies: `Apache Cassandra`, `ScyllaDB`, `Apache HBase`, `Google BigTable`, and `Microsoft Azure Cosmos DB`.
  - Use cases: to store time series data such as sensors, records, history, etc.

- [Document Database](https://aws.amazon.com/nosql/document/): data is stored in JSON-like documents, where each document is a container (array) of key-value pairs (hash tables). 
  - Technologies: `Apache Casandra`, `DynamoDB`, `CouchBase`, `documentDB`, and `MongoDB`.
  - Use cases: suitable for IOT, content management, catalogs, etc.

- [Graph Database](https://aws.amazon.com/nosql/graph/): data entities are represented as nodes and the relationships between them as edges.
  - Technologies: `Neo4j`, `Amazon Neptune`, and `GraphDB`.
  - Use cases: to store social media connections (followers, followed), to store webpages for a WebCrawler, to perform fraud detection, to store data relationships to build recommendation engines, for route optimization, etc.
 
- [In-memory Database](https://aws.amazon.com/nosql/in-memory/): offers fast read and write operations by keeping data in RAM.
  - Technologies: `Redis`, `Memcached`, and `Amazon Elasticache`.
  - Use cases: for caching frequently accessed data such as gaming leaderboards, session stores, activity feed, and real-time data analytics.

# Best practices

- Apply [data denormalization](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/database/05_norm_denorm.md) to store more than one entity in a single table avoiding JOIN operations. For example, `users`, `reactions`, and `photos` can all be stored in a single DynamoDB table.
- Reuse secondary indexes across data types (index overloading).

# Queries

Data from a NoSQL database is often queried using an object-based API.

# ACID or BASE-compliant

Most NoSQL databases are BASE-compliant by default, but not ACID-compliant. MongoDB is ACID-compliant but not BASE-compliant [because transactions are consistent and not eventually consistent, as per the rules of BASE](https://www.mongodb.com/databases/acid-compliance#:~:text=MongoDB%2C%20for%20example%2C%20cannot%20be,per%20the%20rules%20of%20BASE).

# Key Takeaways 

1. NoSQL databases are often referred to as `Not Only SQL` since data stored in a NoSQL database is relational.
2. NoSQL databases do not deprecate RDBMS technologies.
3. Choose NoSQL for OLTP or DSS when scale is a priority. Choose RDBMS technologies for OLAP or OLTP database management systems when scale is not a priority.
