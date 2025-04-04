<div align='center'>
  <h1>Design a Search Engine like Google</h1>
</div>

# Table of Contents

- [Functional Requirements](#functional-requirements)
- [Non-Functional Requirements](#non-functional-requirements)
- [Infrastructure](#infrastructure)
- [Services](#services)
- [Architecture](#architecture)
- [Estimations](#estimations)
  - [Traffic](#traffic)
  - [Storage](#storage)
  - [Bandwidth](#bandwidth)

# Functional Requirements

The user should be able to:

- Search for webpages using phrases or keywords.

# Non-Functional Requirements

The system should be:

- Scalable, prioritizing availability and partition tolerance.
- Reliable (fault-tolerant without losing uploads).
- Designed to handle millions of concurrent connections with fast loading times and minimal latency.

# Infrastructure

1. `Scalability`:

- Scalability requires a horizontally partitioned distributed database. Google currently uses [Bigtable](https://cloud.google.com/bigtable). However, according to the CAP theorem, prioritizing [availability and partition tolerance](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure%2Fdatabase%2F05_CAP_theorem.md) implies that `Cassandra`, `CouchDB`, or [DynamoDB](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/database/06_technologies/DynamoDB.md) can be used.
- NoSQL databases are designed for `horizontal scalability at the database level (sharding)` while having the advantage of avoiding JOIN operations at the cost of redundancy.
- Implement database sharding to split table rows across multiple shards (nodes, servers), improving query performance, scalability, and fault tolerance. [Sharding](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure%2F05_horizontal_scaling.md) can be achieved using a shard key.

2. `Fault Tolerance`:

- Set up database replicas for read-heavy workloads reducing hits to a single database server avoiding single points of failures and enhancing availability and fault tolerance. In AWS, [global tables](https://aws.amazon.com/dynamodb/global-tables/) can be used to replicate DynamoDB tables automatically. Use cold/warm/hot standbys to ensure high availability.

3. `Millions of Concurrent Connections`:

- Set up a load balancer to distribute incoming traffic (requests) across `a fleet of horizontally distributed stateless servers` (e.g., AWS EC2 instances, AWS Lambda functions, or AWS Fargate containers).
- Implement auto-scaling policies to automatically adjust the number of instances based on traffic patterns and resource utilization.
- Use DynamoDB auto scaling (on-demand) mode, instead of provisioned mode, to increase read and write capacity units according to traffic spikes preventing throttling.
- Set up a rate-limiting mechanism using API Gateway to throttle requests when they burst limit (429 Too Many Requests error response).
- Use a load testing tool such as [AWS Distributed Load Testing](https://aws.amazon.com/solutions/implementations/distributed-load-testing-on-aws/) (alternative to [Apache JMeter](https://jmeter.apache.org/) and [Gatling](https://gatling.io/)) to simulate high traffic (heavy load) on servers and identify potential bottlenecks in the system before going live.
- Session Management: store sessions in a caching mechanism (e.g., Redis or ElastiCache) instead of servers for statelessness.

4. `Fast Loading Times and Minimal Latency`:

- Implement a caching mechanism to `reduce latency` by caching the most frequent database queries.

5. `API Gateway:`

- To provide a single entry point for clients to interact with various backend services, handling tasks such as geo-routing, RESTful API definitions, and access control. It also includes the load balancer.

6. `Backend API`:

- The API for client-server communication can be either a [RESTful API](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/backend/restfull_api.md) (implemented with [AWS API Gateway](https://aws.amazon.com/api-gateway/)+[Lambda](https://aws.amazon.com/lambda/)), a [GraphQL API](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/backend/grahql.md) (implemented with [AppSync](https://aws.amazon.com/appsync/)+[Amplify](https://aws.amazon.com/amplify/) or [graphql-http](https://graphql.org/blog/2022-11-07-graphql-http/)), or [gRPC](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/backend/gRPC.md).

7. `Deployment`:

- AWS Beanstalk for serverless deployment of long-running containerized or non-containerized applications.

- If more control over the infrastructure is required, use Docker to containerize the Express or Apollo server for ECS (Elastic Container Service) or EKS (Elastic Kubernetes Service), and deploy it using AWS Fargate.

# Services

1. `Frontend Service:` (UX/UI) can be implemented with React.js.
2. `Web Crawler Service:` used to fetch web pages and store compressed crawled data in a distributed file system (e.g., [Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) or [AWS S3](https://aws.amazon.com/s3/)).
3. `Parser Service:` used to parse the raw HTML data fetched by the crawler to extract meaningful content such as text, links, metadata, and other signals.
4. `Storage Service`: used to store parsed data (e.g., [AWS S3](https://aws.amazon.com/s3/)).
5. `Indexer`: used to map documents to a vocabulary of tokens/keywords or other signals (e.g., formating, position). This can be implemented with a NoSQL key-value database ([DynamoDB](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/database/06_technologies/DynamoDB.md) or [Bigtable](https://cloud.google.com/bigtable)).
6. `Page Ranking Service:` to rank documents retrieved by the query service based on their relevance to the user's search keyword. TF-IDF (Term Frequency-Inverse Document Frequency) is not a suitable choice if searching is to be made in a large corpus (the entire web).
7. `Inverted Index (Posting List) Service:` used to map each keyword/token in the vocabulary to a list containing references/pointers/indexes to the respective documents/webpages containing that keyword. An [AVL tree](https://github.com/camponogaraviera/ds-and-algo/blob/dev/theory/data_structures/08_trees/4_avl_tree.md) can be used to store the tokens and their corresponding lists of indexes. `An AVL tree is always a balanced Binary Search Tree` which has faster lookup-by-value operations than a Red-Black Tree and it is suitable for lookup-intensive (search) tasks.
8. `Caching Service`: to `reduce latency` by caching the most frequently queried search terms, web pages, and search indexes from the `inverted indexer`. Technologies: [Amazon Elasticache](https://aws.amazon.com/pm/elasticache/) (general-purpose), or [AWS DAX](https://aws.amazon.com/dynamodb/dax/) (purpose-built for DynamoDB). This caching solution stays in the AWS cloud.
9. `Query Service:` to handle user search queries, retrieving and returning relevant documents from the index.

# Architecture

A monolithic architecture can be a starting point for prototyping and product validation.

As the system scales and matures, [microservices architecture with Saga pattern](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/10_patterns.md#saga-pattern) is the way to go for isolation and data consistency in a distributed system/application where business transactions span multiple microservices. The saga workflow can be implemented serverless with `AWS API Gateway`, `AWS Step Functions`, `AWS Lambda`, and [Amazon Keyspaces](https://aws.amazon.com/pt/keyspaces/) which is `compatible with Apache Cassandra`.

# Estimations

## Traffic

Suppose the system has 1B DAUs, and each user makes 8 searches per day on average.

- Search requests per day:

$$
1B \space (users) \times 8 \space (searches) = 8B \space searches/day
$$

- Number of Requests Per Second (RPS):

$$
\frac{8B}{(24 \times 3600 \space seconds)} \sim 92,593 \space RPS
$$

## Storage

Suppose 1M websites of 1MB are crawled per day, on average.

- Daily storage usage = 1M websites \* 0.001GB/website = 1,000 GB/day or 1 TB/day.
- Monthly storage usage = 1 TB/day \* 30 days = 30 TB/month.

## Bandwidth

The Bandwidth (data transfer) considering an ingress of 1 TB of data stored per day:

$$
\frac{1 \space TB}{(24 \times 3600 \space seconds)} \sim 12 \space MB/second
$$
