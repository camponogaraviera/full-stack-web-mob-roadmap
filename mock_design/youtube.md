<div align='center'>
  <h1>Design a YouTube-like On-Demand Video Streaming Service</h1>
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
- [Normalized Database Design](#normalized-database-design)
  - [Entity-Relationship Diagram (ERD)](#entity-relationship-diagram-erd)
  - [Entity Chart](#entity-chart)
  - [Single Relational Database Design](#single-relational-database-design)
- [Denormalized Database Design](#denormalized-database-design)
  - [DynamoDB Access (Query) Patterns](#dynamodb-access-query-patterns)
  - [DynamoDB Single Table Design](#dynamodb-single-table-design)
- [AWS Workflow](#aws-workflow)
- [AWS Pricing](#aws-pricing)

# Functional Requirements

The user should be able to:

- Sign up and Sign in with email and password.
- Upload, encode and transcode a video.
- Search for a video.
- Stream and watch a video-on-demand or live.
- Submit reviews/feedback.

# Non-Functional Requirements

The system should be:

- Scalable, prioritizing availability and partition tolerance.
- Reliable (fault-tolerant without losing uploads).
- Designed to handle millions of concurrent connections with fast loading times and minimal latency.

# Infrastructure

1. `Scalability`:

- Scalability requires a horizontally partitioned distributed database to store video metadata (title, description, URL location, image thumbnail, upload date, view count, etc.). YouTube currently uses `MySQL` with [Vitess](https://vitess.io/). However, according to the CAP theorem, prioritizing [availability and partition tolerance](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure%2Fdatabase%2F05_CAP_theorem.md) implies that `Cassandra`, `CouchDB`, or [DynamoDB](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/database/06_technologies/DynamoDB.md) can be used.
- NoSQL databases are designed for `horizontal scalability at the database level (sharding)` while having the advantage of avoiding JOIN operations at the cost of redundancy.
- Implement database sharding to split table rows across multiple shards (nodes, servers), improving query performance, scalability, and fault tolerance. [Sharding](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure%2F05_horizontal_scaling.md) can be achieved using a shard key.

2. `Fault Tolerance`:

- Set up database replicas for read-heavy workloads reducing hits to a single database server avoiding single points of failures and enhancing availability and fault tolerance. In AWS, [global tables](https://aws.amazon.com/dynamodb/global-tables/) can be used to replicate DynamoDB tables automatically. Use cold/warm/hot standbys to ensure high availability.
- To avoid hotspots when a video goes viral and enhance availability, temporarily store the video in a CDN.
- Configure a dedicated compute instance to handle peak traffic with sufficient Read Capacity Units to avoid [Throttling](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/08_celebrity.md).

3. `Millions of Concurrent Connections`:

- Set up a load balancer to distribute incoming traffic (requests) across `a fleet of horizontally distributed stateless servers` (e.g., AWS EC2 instances, AWS Lambda functions, or AWS Fargate containers).
- Implement auto-scaling policies to automatically adjust the number of instances based on traffic patterns and resource utilization.
- Use DynamoDB auto scaling (on-demand) mode, instead of provisioned mode, to increase read and write capacity units according to traffic spikes preventing throttling.
- Set up a rate-limiting mechanism using API Gateway to throttle requests when they burst limit (429 Too Many Requests error response).
- Use a load testing tool such as [AWS Distributed Load Testing](https://aws.amazon.com/solutions/implementations/distributed-load-testing-on-aws/) (alternative to [Apache JMeter](https://jmeter.apache.org/) and [Gatling](https://gatling.io/)) to simulate high traffic (heavy load) on servers and identify potential bottlenecks in the system before going live.
- Session Management: store sessions in a caching mechanism (e.g., Redis or ElastiCache) instead of servers for statelessness.

4. `Fast Loading Times and Minimal Latency`:

- Implement a Content Delivery Network (CDN) to store static data (viral videos) on servers geographically closer to end users, `reducing the load` on origin servers and speeding up content delivery.
- Implement a caching mechanism to `reduce latency` by caching the most frequent database queries.

5. `API Gateway:`

- To provide a single entry point for clients to interact with various backend services, handling tasks such as geo-routing, RESTful API definitions, and access control. It also includes the load balancer.

6. `Backend API`:

- The API for client-server communication can be either a [RESTful API](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/backend/restfull_api.md) (using [AWS API Gateway](https://aws.amazon.com/api-gateway/)+[Lambda](https://aws.amazon.com/lambda/)), a [GraphQL API](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/backend/grahql.md) (using [AppSync](https://aws.amazon.com/appsync/)+[Amplify](https://aws.amazon.com/amplify/) or [graphql-http](https://graphql.org/blog/2022-11-07-graphql-http/)), or [gRPC](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/backend/gRPC.md).

7. `Deployment`:

- AWS Beanstalk for serverless deployment of long-running containerized or non-containerized applications.

- If more control over the infrastructure is required, use Docker to containerize the Express or Apollo server for ECS (Elastic Container Service) or EKS (Elastic Kubernetes Service), and deploy it using AWS Fargate.

# Services

1. `Frontend Service:` (UX/UI) can be implemented with React.js.

2. `Security Service`: login, authentication, and authorization can be implemented with [AWS Cognito](https://aws.amazon.com/pm/cognito/) and [AWS IAM](https://aws.amazon.com/iam/), respectively. Cognito automatically handles the storage of user credentials (e.g., passwords, tokens) and metadata (e.g., email, phone number, username) inside **Cognito User Pools**.

3. `Storage Service`: [AWS S3](https://aws.amazon.com/s3/) can be used to store raw videos, post-processed videos, and other binary data with auto replication.

4. `Database Service`: DynamoDB can be used to store video metadata (title, description, URL location, image thumbnail, upload date, view count, etc.), user preferences/settings, comments, and likes.

5. `Media Service`: for video upload and pre-processing (encoding and transcoding), a message queue (AWS SQS) can be used to handle encoding and transcoding requests and to send videos to a domain-specific service (possibly a fleet of servers). Post-processed videos are then stored in AWS S3. Available AWS services are:

   - [AWS Elemental MediaLive](https://aws.amazon.com/medialive/): real-time encoder used to convert a raw live video file into a compressed digital format. It does not deliver content to end users.
   - [Amazon Elastic Transcoder](https://aws.amazon.com/elastictranscoder/): basic audio/video transcoding service used to convert an already encoded video from one format to another.
   - [AWS Elemental MediaConvert](https://aws.amazon.com/mediaconvert/): professional-grade transcoder used to convert image and video files in AVC (H.264 or MPEG-4 Part 10) media format.
   - Custom Python functions for data compression can be implemented within AWS Lambda.

6. `Streaming Service:`

   - [AWS Elemental MediaPackage](https://aws.amazon.com/pt/mediapackage/): for on-demand video streaming with DVR features (restart, pause, rewind) using offset. It also packages content for different streaming formats.
   - [AWS CloudFront](https://aws.amazon.com/cloudfront): to deliver content to end users (live streaming).

7. `Typical Streaming AWS Pipeline:`

```bash
Raw Video Source → MediaLive (to encode/compress) → MediaPackage (to convert to a streaming format) → CloudFront (content delivery network) → End Users
```

[AWS IVS](https://aws.amazon.com/ivs/) can handle the entire video pipeline from ingestion to delivery.

8. `Search Engine Service:` the search engine can be implemented with: [Elasticsearch](https://www.elastic.co/elasticsearch), [AWS OpenSearch](https://aws.amazon.com/what-is/opensearch/), and [AWS CloudSearch](https://aws.amazon.com/cloudsearch/).

9. `URL Sharing Service:` users can share their favorite videos using a `URL shortener service`.

10. `CDN Service`: used to speed up `content delivery` and mitigate the `celebrity problem`. The CDN takes static data from [AWS S3 Storage](https://aws.amazon.com/s3/). Technologies: [AWS CloudFront](https://aws.amazon.com/cloudfront/).

11. `Caching Service`: [Amazon Elasticache](https://aws.amazon.com/pm/elasticache/) (general-purpose), or [AWS DAX](https://aws.amazon.com/dynamodb/dax/) (purpose-built for DynamoDB). This caching solution stays in the AWS cloud.

# Architecture

1. A monolithic architecture can be a starting point for prototyping and product validation.

2. As the system scales and matures, [microservices architecture with Saga pattern](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/10_patterns.md#saga-pattern) is the way to go for isolation and data consistency in a distributed system/application where business transactions span multiple microservices. The saga workflow can be implemented serverless with `AWS API Gateway`, `AWS Step Functions`, `AWS Lambda`, and [Amazon Keyspaces](https://aws.amazon.com/pt/keyspaces/) which is `compatible with Apache Cassandra`.

# Estimations

## Traffic

Suppose the system has 122M DAUs, and each user searches/watches 10 videos per day on average.

- Search requests per day:

$$
122M \space (users) \times 10 \space (searches) = 1.22B \space searches/day
$$

- Number of Requests Per Second (RPS):

$$
\frac{1.22B}{(24 \times 3600 \space seconds)} \sim 14,120 \space RPS
$$

## Storage

Suppose each user uploads 5 videos of 100MB per day (0.5GB/user), on average.

- Daily storage usage for 500k DAU = 500k users \* 0.5GB/user = 250,000 GB/day or 250 TB/day.
- Monthly storage usage = 250 TB/day \* 30 days = 7,500 TB/month.

## Bandwidth

The Bandwidth (data transfer) considering an ingress of 250 TB of data stored per day:

$$
\frac{250 \space TB}{(24 \times 3600 \space seconds)} \sim 2,894 \space MB/second
$$

# Normalized Database Design

Unlike a microservice architecture, where services are decoupled and independently deployed, a monolithic architecture consolidates all business logic within a single application/codebase deployed as a unit.

Within a monolithic architecture, it is common to have a single relational database for the entire application, which might include multiple tables that interact through JOIN operations.

## Entity-Relationship Diagram (ERD)

...

## Entity Chart

- In a relational database, each entity is represented by a table.
- In a NoSQL database, entities are the nouns of the application.

A minimal version of the system has the following entity chart:

| Entity  | Primary Key: PK | Sort Key: SK  |
| ------- | --------------- | ------------- |
| User    | USER#username   | USER#username |
| Videos  | VIDEO#VideoID   | VIDEO#VideoID |
| Reviews | VIDEO#VideoID   | REV#ReviewID  |

## Single Relational Database Design

1. Users Table:

   - Primary Key: userID (uuid)
   - name (varchar)
   - email (varchar)
   - role (varchar) - Member or Admin
   - createdAt (timestamp)

2. Videos Table:

   - Primary Key: videoID (uuid)
   - userID (uuid) - Foreign Key referencing Users table
   - videoTitle (varchar)
   - description (varchar)
   - thumbURL (varchar or JSON) - JSON array for storing thumbnail URL
   - videoURL (varchar or JSON) - JSON array for storing video URL
   - uploadedAt (timestamp)

3. Reviews Table:
   - Primary Key: reviewID (uuid)
   - userID (uuid) - Foreign Key referencing the Users table
   - videoID (uuid) - Foreign Key referencing Videos table
   - rating (int)
   - comment (varchar)
   - createdAt (timestamp)

# Denormalized Database Design

In a microservice architecture, one should prioritize the [database-per-service-pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-data-persistence/database-per-service.html).

Within this architecture, it is common to use a denormalized single table design for each individual microservice.

## DynamoDB [Access (Query) Patterns](https://docs.aws.amazon.com/prescriptive-guidance/latest/dynamodb-data-modeling/step3.html)

Access patterns are required to be known before modeling DynamoDB Tables. Recall that when there is more than one entity and a `fetch many` access pattern, a composite primary key should be used to satisfy the required access pattern.

1. Register a User/Sign up unique on both username and email address (**not required when using Amazon Cognito**):

   - Partition Key (PK): `USER#username`
   - Sort key (SK): `USER#username`
   - Attributes: `Username`, `Email`, `Name`, `Role`, `DateJoined`, etc.
   - Post: use `PutItem` with `PK` and `SK` both as `USER#username`.

2. Sign in with email and password (**not required when using Amazon Cognito**):

   - GSI1:
     - Partition Key (PK): `email`
     - Sort Key (SK): `USER#username`
     - Query: use `Query` on GSI1 with `PK` as `email`.

3. Upload a Video:

   - Partition Key (PK): `VIDEO#VideoID`
   - Sort key (SK): `VIDEO#VideoID`
   - Attributes: `VideoID`, `VideoTitle`, `Description`, `Timestamp`, `URL`, etc.
   - Post: use `PutItem` with `PK` and `SK` both as `VIDEO#VideoID`.

4. Search for a Video:

   - GSI2:
     - Partition Key (PK): `Description`
     - Sort Key (SK): `VIDEO#VideoID`
     - Query: use `Query` on GSI2 with `PK` as the partial or full video `Description`.

5. Fetch all Videos from a particular User:

   - Partition Key (PK): `USER#username`
   - Sort key (SK): `VIDEO#VideoID`
   - Attributes: `VideoID`, `VideoTitle`, `Description`, `Timestamp`, `URL`, etc.
   - Get: use `Query` with `PK` as `USER#username` and a sort key condition starting with `VIDEO#`.

6. Fetch all Reviews from a particular Video:
   - Partition Key (PK): `VIDEO#VideoID`
   - Sort key (SK): `REV#ReviewID`
   - Attributes: `Username`, `Rating`, `Feedback`, etc.
   - Get: use `Query` with `PK` as `VIDEO#VideoID` and a sort key condition starting with `REV#`.

## DynamoDB Single Table Design

- It is **not** a best practice to use a single database design where all microservices share the same table (a.k.a hot table in this case). This will be shown here for simplicity.

- As discussed in the DynamoDB section, contrary to relational databases, DynamoDB does not support join operations by design. While not recommended, it is possible to simulate/fake a join by performing multiple key-value lookups. For example, one might first fetch a record and then make additional requests to retrieve related records. This pattern should be avoided as much as possible since `network I/O introduces latency and multiple queries increase computational (CPU) cost`.

- With Moore's law closing to an end, CPU operations will end up being more expensive than memory storage on hard drives. Therefore, data redundancy introduced in a single-table design is justifiable as the system scales. The design philosophy of DynamoDB emphasizes a `single-table model through data denormalization to avoid the need for multiple queries`. By using a composite primary key and a single-table design, one can effectively "join" related data without performing multiple queries.

- Recall that access patterns such as `fetch a user and videos from user` requires a `pre-join`, i.e., the Video items should belong to the same partition key (same item collection) as the parent item (e.g., USER#username). This allows for fetching Users and Videos in a single request.

- In the following table, PK and SK are used as generic names to represent the partition key and sort key, respectively. This is called `primary key overloading`. Also, a prefix (e.g., USER#, VIDEO#, REV#) is added to each value of a partition key and sort key to avoid overlapping between items with the same name (e.g., a user and a video with the same name), since a primary key must be unique across items. And the prefix `#` in #VIDEO# is used to ensure that videos are fetched in descending order.

<table>
  <tr> 
    <td colspan="2"> Primary Key </td>
    <td colspan="5">Attributes</td>
  </tr>
  <tr>
    <th>Partition Key: PK</th>
    <th>Sort Key: SK</th>
    <th>Attribute 1</th>
    <th>Attribute 2</th>
    <th>Attribute 3</th>
    <th>Attribute 4</th>
    <td>Attribute 5</td>
  </tr>
  <tr> 
  </tr> 
  <tr>
    <td rowspan="2">USER#jamesbond</td>
    <td colspan="1">  </td>
    <td> Username </td>
    <td> Email </td>
    <td> Name </td>
    <td> Role </td>
    <td> DateJoined </td>
  </tr>
  <tr>
    <td>USER#jamesbond</td>
    <td>"jamesbond"</td>
    <td>"jamesbond@example.com"</td>
    <td>"James Bond"</td>
    <td>"admin"</td>
    <td>2024-06-01T00:00:00Z</td>
  </tr>
  <tr>
    <td rowspan="4">Item Collection<br>USER#sherlock</td>
    <td colspan="1">  </td>
    <td>VideoID</td>
    <td>VideoTitle</td>
    <td>Description</td>
    <td>Timestamp</td>
    <td>URL</td>
  </tr>
  <tr>
    <td>#VIDEO#001</td>
    <td>001</td>
    <td>"Sherlock Holmes"</td>
    <td>"Granada Television"</td>
    <td>2024-06-01T10:00:00Z</td>
    <td>{url: '...'}</td>
  </tr>
  <tr>
    <td colspan="1">  </td>
    <td> Username </td>
    <td> Email </td>
    <td> Name </td>
    <td> Role </td>
    <td> DateJoined </td>
  </tr>
  <tr>
    <td>USER#sherlock</td>
    <td>"sherlock"</td>
    <td>"sherlock@example.com"</td>
    <td>"Sherlock Holmes"</td>
    <td>"user"</td>
    <td>2024-06-01T00:00:00Z</td>
  </tr>
  <tr>
    <td rowspan="2">VIDEO#001</td>
    <td colspan="1">  </td>
    <td> Username </td>
    <td> Rating </td>
    <td> Feedback </td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>REV#001</td>
    <td>"jamesbond"</td>
    <td>5</td>
    <td>"Superb!"</td>
    <td></td>
    <td></td>
  </tr>
</table>

# AWS Workflow

- Workflow:
  - Web & Mob Applications, IoT devices =>
    - => [API Gateway](https://aws.amazon.com/api-gateway/) (load balancer, geo-routing, RESTful API definitions, and access control)
      - => [Amazon Cognito](https://aws.amazon.com/cognito/) (CIAM such as sign up, sign in, IAM roles, etc.)
      - => [AWS IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/intro-structure.html) (user access/authorization to different AWS services)
      - => [Amazon CloudFront](https://aws.amazon.com/cloudfront/) (the CDN).
        - => [Amazon S3](https://aws.amazon.com/s3/) (data storage).
      - => AWS Lambda (functions)
        - => DynamoDB (database) => DynamoDB Stream (logging DynamoDB changes)
        - => Amazon SNS (mobile push notifications)
        - ...

# AWS Pricing

Try the [AWS Calculator](https://calculator.aws/#/addService) to get a quotation for each AWS service used.
