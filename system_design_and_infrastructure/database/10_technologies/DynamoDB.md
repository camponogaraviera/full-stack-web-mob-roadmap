<div align='center'>
  <h1>DynamoDB</h1>
</div>

# Table of Contents

- [Table of Contents](#table-of-contents)
- [About](#about)
- [Components](#components)
  - [Table](#table)
  - [Item](#item)
  - [Attributes](#attributes)
  - [Partitions](#partitions)
  - [Keys](#keys)
  - [Indexes](#indexes)
- [API Actions](#api-actions)
- [Ways of interacting with DynamoDB](#ways-of-interacting-with-dynamodb)
- [Read and Write Capacity Units](#read-and-write-capacity-units)
  - [Calculating RCUs, WCUs, and Partitions](#calculating-rcus-wcus-and-partitions)
    - [Example](#example)
- [Data Modeling Best Practices](#data-modeling-best-practices)
- [Key Takeaways](#key-takeaways)
- [DynamoDB costs](#dynamodb-costs)
- [Summary of Limitations](#summary-of-limitations)
- [References](#references)

# About

DynamoDB is a JAVA application deployed to EC2 instances a.k.a nodes. It is a serverless, fully managed, NoSQL Key-Value store that provides low latency (single-digit millisecond) performance. DAX (in-memory caching system) can be used to achieve microsecond performance. Additionally, it supports semi-structured data (hence flexible) and is considered schema-less implying that table attributes are not required to be defined upfront. This contrasts with SQL databases. It is also fault-tolerant, in the sense that data can be replicated across a fleet of servers (AZs). Data is also encrypted at rest and in transit. Provides "global tables", a multi-master multi-region version of DynamoDB. Follows a pay-as-you-go billing plan, and has a PITR (point-in-time recovery) for backup and restore.

With the new added support for transactional consistency, DynamoDB has become partially ACID-compliant. CRUD operations are performed through APIs and Tables must be defined a priori.

DynamoDB does not support:

1. Table JOINs (a.k.a JOIN clause): query operations that combine rows from two or more tables based on related columns (fields) between them through foreign keys.
2. Analytical operations: data aggregation, complex filtering, and statistical analysis.

# Components

## Table

A Table in DynamoDB is a collection of records of items, similar to a collection in MongoDB. While a Table in a relational database might include a single entity type, a single DynamoDB Table allows multiple entity types through a `single table design`. For example, in a relational database, entities such as `Customer` and `Orders` are split into individual tables, and queries would be performed through JOIN operations via foreign keys. DynamoDB avoids JOIN operations by combining all entities in a single Table. While this might cause data redundancy, it reduces the number of round trips to get the needed data.

## Item

An item in a DynamoDB table is a document, it can be compared to a row (single record) in a relational database or a key-value pair in a hash table. Every item in a DynamoDB table is uniquely identified by its primary key, which consists of a partition key and a sort key (optionally), and every item might contain one or more attributes of different data types. Each item is upper bounded by a maximum size of 400KB, while the maximum size of a partition is 10 GB. ["In a table with one or more local secondary indexes, each item collection is stored in one partition. The total size of such an item collection is limited to the capability of that partition: 10 GB. For an application where the data model includes item collections which are unbounded in size, or where you might reasonably expect some item collections to grow beyond 10 GB in the future, you should consider using a global secondary index instead."](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/LSI.html#LSI.ItemCollections.SizeLimit)

- Item collection: a collection of items that share/belong to/(are located in) the same partition.

Example of a DynamoDB table with a composite primary key containing a record of three items with four attributes:

<table>
  <tr> 
    <td colspan="2"> Primary Key </td>
    <td colspan="4">Attributes</td>
  </tr>
    <th>Partition Key: PK</th>
    <th>Sort Key: SK</th>
    <th>Attribute 1</th>
    <th>Attribute 2</th>
    <th>Attribute 3</th>
    <th>Attribute 4</th>
  </tr>
  <tr>  
  </tr>	
  <tr>
    <td rowspan="6">Item Collection<br>USER#sherlock</td>
    <td colspan="1">  </td>
    <td>VideoTitle</td>
    <td>Description</td>
    <td>Timestamp</td>
    <td>...</td>
  </tr>
  <tr>
    <td>VIDEO#001</td>
    <td>The Sign of Four</td>
    <td>Granada Television</td>
    <td>2024-06-01T10:00:00Z</td>
    <td>...</td>
  </tr>
  <tr>
    <td colspan="1">  </td>
    <td>VideoTitle</td>
    <td>Description</td>
    <td>Timestamp</td>
    <td>...</td>
  </tr>
  <tr>
    <td>VIDEO#002</td>
    <td>The Hound of the Baskervilles</td>
    <td>Granada Television</td>
    <td>2024-06-02T10:00:00Z</td>
    <td>...</td>
  </tr>
  <tr>
    <td colspan="1">  </td>
    <td> Username </td>
    <td> Email </td>
    <td> Name </td>
    <td> Role </td>
  </tr>
  <tr>
    <td>USER#sherlock</td>
    <td>sherlock</td>
    <td>sherlock@example.com</td>
    <td>Sherlock Holmes</td>
    <td>Admin</td>
  </tr>
</table>

## Attributes

Attributes are elements of an item. For example, a Car item has several attributes, such as model, year, and color. Attributes are comparable to columns (fields) in a relational database table, or keys in a hash table.

- Attributes can be classified into:

  - Nested attributes: are the ones defined in a complex type (list or map).
  - Top-level attributes: are the ones defined in a simple/scalar type.
  - Projected attributes: ["Represents attributes that are copied (projected) from the table into an index. These are in addition to the primary key attributes and index key attributes, which are automatically projected."](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Projection.html).

- Attribute data types can be classified into:

  - Scalar types: string, number, binary, boolean, and null.
  - Set types: string set, number set, and binary set. They are unordered collections of strings, numbers, or binary. All values within a set should be of the same data type. No duplicates allowed.
  - Complex (a.k.a document) types: list and map. They can be nested up to 32 levels deep. Maps are unordered collections of Key-Value pairs with no data type restrictions (used to store JSON documents).

## Partitions

When a DynamoDB table is created, partitions (a.k.a nodes) are automatically allocated to the table for storing data. Items written to the table are then automatically sharded across multiple partitions. Each partition can contain several items that are identified by a partition key (a.k.a hash key) and an optionally sort key (a.k.a range key).

Each partition can store up to 10GB of data (item collection) and deliver a maximum throughput of 1000 WCUs and 3000 RCUs. Items belonging to the same item collection are located in the same partition.

The number of partitions can be determined by the provisioned capacity.

## Keys

In DynamoDB, a primary key is a mandatory partition key of a Table, and it is used to uniquely identify an item. It can be classified into two types:

- Simple primary key: uses a single partition key (a.k.a hash key) that uniquely identifies an item.
- Composite primary key: uses a combination of partition key + sort key (a.k.a range key).
  - Partition key: is used to segment and distribute items across shards. This allows one to store multiple items/entities in an item collection under the same partition.
  - Sort key: the sort key attribute enables complex range queries against items in a partition. A partition can be thought of as a folder/bucket that contains items, and the sort key orders the items in that folder.

A partition key and a sort key can `only be of scalar data types`: string, number, binary, boolean, and null. Complex attributes such as `Map and List are not supported for use as partition key or sort key`.

When using binary or string as index attributes, they should be <2KB for the hash key (primary key) and <1KB for the sort key.

Queries are made only to the partition key and sort key. Therefore, it is important to identify the access patterns before modeling DynamoDB Tables. Example: suppose a query pattern (or [access pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/dynamodb-data-modeling/step3.html)) should fetch all the customer's orders in the last 24hrs. In this case, the partition key can be the customer's ID and the sort key can be the purchase/order date.

When using more than one type of entity in a DynamoDB table, the primary key and the sort key should be identified by generic names such as PK and SK instead of names such as "username" or "date". When using a single type of entity in a DynamoDB table, there is no need to use a generic key such as PK for the primary key. Entity template is also useful to represent items under a primary or sort key.

## [Indexes](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SecondaryIndexes.html)

A Local Secondary Index (LSI) and a Global Secondary Index (GSI) are used to create alternative access patterns beyond those available with a primary composite key.

- Local Secondary Index (LSI): must be defined when creating a DynamoDB table and cannot be modified or removed later. It uses the `same partition key` as the table's primary key, but a different sort key. It does not have its own provisioned throughput capacity, instead, it `shares the read and write capacity units` (RCUs and WCUs) of the table. There can be only `up to 5 local secondary indexes`. The maximum item (partition) size allowed is `limited to <10GB`. Queries on an LSI are `limited to a single partition` of the table by its partition key (hash key). Queries can be made using either `eventually consistent` or `strongly consistent` reads. The primary key should be `composite`.

- Global Secondary Index (GSI): used when item collections are expected to grow beyond 10 GB. It can be created and deleted anytime. Any attribute from the table can be used as a partition key or a sort key. As opposed to a local secondary index, a GSI has its own partition, i.e., it `does not share RCUs and WCUs throughput` with the DynamoDB table. Although it might increase cost, it allows more control over provision capacity. There's a limit of `5 global secondary indexes per table`. There is `no size limit for partitions`. Queries on a GSI can be made `across all partitions`. Queries can be made only with `eventual consistency`, and there is `no support for strongly consistent reads`. Only projected attributes are returned. The primary key can be either `simple or composite`. The provision of the GSI must match the throughput of the table.

Recall `consistency` from the CAP theorem. [Eventual consistency](https://docs.aws.amazon.com/whitepapers/latest/comparing-dynamodb-and-hbase-for-nosql/consistency-model.html) in DynamoDB (default option) means the response from a read request might contain stale data (not the latest write). "Consistency across all copies of data is usually reached within a second."

[Strongly consistent read](https://docs.aws.amazon.com/whitepapers/latest/comparing-dynamodb-and-hbase-for-nosql/consistency-model.html) in DynamoDB ensures the response contains the latest/most up-to-date data. It takes more resources than eventually consistent read to be processed, consuming twice as much RCU.

# API Actions

- Query API operation: used to retrieve/fetch multiple items from a common partition key (from a single item collection) on either the base/main table or the secondary index. The partition key must be included in every request even though conditions can be imposed to the sort key. Useful in one-to-many and many-to-many relationship.

- Scan API operation: used to retrieve/fetch all items in a table by scanning through all its partitions. Suitable for small tables only when required to retrieve data without specifying a particular key, and for exporting data from the table to another system. When the table is too large, it also returns the current pagination key, and from that moment onwards another call has to be made to fetch the remaining data, and so on.

- Item-based actions: used to search for a single specific item based on a provided primary key in its entirety.
  - [GetItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_GetItem.html): to read an item from the table.
  - [PutItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html): to write an item to the table.
  - [UpdateItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html): to update an item (adding, removing, or modifying properties) of the table, or to create one if it does not exist.
  - [DeleteItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_DeleteItem.html): to delete an item from the table.
- Batch actions: used to read and write multiple items in a single request. Read and write can succeed or fail independently.

  - [BatchGetItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchGetItem.html).
  - [BatchWriteItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchWriteItem.html).

- Transaction actions: used to read and write multiple items in a single request. Read and write can succeed or fail concurrently.

- Query and Scan operations can fetch a maximum of 1MB per request, and they return a `LastEvaluatedKey` property to read the next page in the subsequent request (pagination).

# Ways of interacting with DynamoDB

- For database administration:

  - AWS management console.
  - AWS CLI.

- For application management:
  - AWS SDK.

# Read and Write Capacity Units

When creating a DynamoDB table, it is possible to set up custom RCUs and WCUs.

During heavy read or write workloads, i.e., when a query on a database index exceeds the database provisioned capacity throughput (RCUs and WCUs), DynamoDB throttles the request, meaning that requests will be either delayed or rejected with a `ProvisionedThroughputExceededException`.

Throttling prevents excessive resource usage by a single workload, ensuring the system remains available and responsive for other requests. Nevertheless, DynamoDB allows auto-scaling for provisioned capacity mode, adjusting RCUs/WCUs dynamically based on demand to reduce throttling risks.

## Calculating RCUs, WCUs, and Partitions

- 1 capacity unity (RCU or WCU) can handle only 1 RPS (request per second).

- 1 RCU: enables 1 strongly consistent read or 2 eventually consistent reads with items in memory blocks of 4KB.
- 1 WCU: enables 1 write per second with items in memory blocks of 1KB.
- Maximum number of read capacity units (RCU) per second in a single partition:
  - Strongly consistent read: 3000 RCU in chunks of 4KB. Equivalent to `12MB of data per second`.
  - Eventually consistent read: 6000 RCU in chunks of 4 KB. Equivalent to `24MB of data per second`.
- Maximum number of write capacity units (WCU) per second in a single partition: 1000 WCU in chunks of 1KB. Equivalent to `1MB of data per second`.

- Read Provisioned Throughput:
  - Projected attribute: 1 RCU with one strongly consistent read and two eventually consistent reads per second for items of size <4KB.
  - Non-projected attribute: 1 RCU to read from the index and 1 RCU to read from the table.
- Write Provisioned Throughput: 1 WCU to insert, delete, and update projected attributes, and 2 WCUs to update items for key attributes.

### Example

Suppose that items in a DynamoDB table are of size 10KB. And the provisioned capacity throughput for the table is 100 RCUs and 100 WCUs. The amount of information that can be retrieved is:

- Strongly consistent read throughput: 4KB \* 100RCUs = 400KB/s
- Eventually consistent read throughput: 2 \* 4KB \* 100RCUs = 800KB/s
- Write throughput: 1KB \* 100WCUs = 100KB/s

The number of partitions is (100 RCUs/3000 + 100 WCUs/1000) = 1 (rounded up).

# Data Modeling Best Practices

One always seeks a single table design per microservice/application. A rule of thumb is to:

1. - Draw an entity-relationship diagram (ERD).
2. - Draw the entity chart.
   - In a relational database, entities are tables.
   - In a NoSQL database, entities are the nouns of the application.
3. - Identify the access patterns.
4. - Identify the composite primary keys.
5. - When the composite primary keys are not enough, handle the remaining access patterns with secondary indexes (LSI and GSI) and streams.

# Key Takeaways

1. DynamoDB is schema-less, i.e., it is possible to update and add a new attribute to a DynamoDB table after it has been created. This can be achieved through an [UpdateItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html) and a [PutItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html) (or [BatchWriteItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchWriteItem.html)) operation, respectively, using the AWS SDK.

2. While DynamoDB is considered schema-less, a schema is still needed at the application level.

3. In DynamoDB, JOINs are not supported by design to avoid excessive round trips between different Tables during querying. Data should undergo denormalization. By using a composite primary key and a single-table design, one can effectively "join" related data without performing multiple queries.

4. While access patterns are required to be known before modeling DynamoDB tables, they can be changed in the future. However, changes to access patterns may require data migration strategies, which can involve creating new tables or restructuring existing ones such as adding/updating attributes and global secondary indexes.

# [DynamoDB costs](https://docs.aws.amazon.com/wellarchitected/latest/serverless-applications-lens/capacity.html)

DynamoDB has two pricing models:

- On-demand pricing: you pay per usage of WCUs and RCUs. This happens when using the autoscale feature.
- Provisioned capacity mode: you pay for pre-defined provisioned WCUs and RCUs.

There is a free tier with a maximum of 25 WCUs and 25 RCUs. This enables handling up to 200M requests per month, equivalent to:

$$
200M/(30 * 24 * 60 * 60) = 77.16 RPS (requests/second).
$$

- `Eventually consistent reads` are half the cost of `strongly consistent reads`.

# Summary of Limitations

- Maximum item size: 400KB

- Maximum partition size: 10GB

- Query and Scan operations can fetch a maximum of 1MB per request, and they return a `LastEvaluatedKey` property to read the next page in the subsequent request (pagination).

- Maximum number of read capacity units (RCU) per second in a single partition:
  - Strongly consistent read: 3000 RCU in chunks of 4KB. Equivalent to 12MB of data per second.
  - Eventually consistent read: 6000 RCU in chunks of 4 KB. Equivalent to 24MB of data per second.
- Maximum number of write capacity units (WCU) per second in a single partition: 1000 WCU in chunks of 1KB. Equivalent to 1MB of data per second.

# References

[1] [AWS re:Invent 2018: Amazon DynamoDB Deep Dive: Advanced Design Patterns for DynamoDB (DAT401)](https://www.youtube.com/watch?v=HaEPXoXVf2k).

[2] [Best practices for designing and architecting with DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/best-practices.html).
