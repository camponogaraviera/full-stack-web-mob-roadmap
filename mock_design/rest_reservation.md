<div align='center'>
  <h1>Design a Restaurant Reservation System</h1>
</div>

# Table of Contents

- [Functional Requirements](#functional-requirements)
- [Non-Functional Requirements](#non-functional-requirements)
- [Infrastructure](#infrastructure)
- [Services](#services)
- [Architecture](#architecture)
  - [AWS Step Functions](#aws-step-functions)
- [Estimations](#estimations)
  - [Traffic](#traffic)
  - [Storage](#storage)
  - [Bandwidth](#bandwidth)
- [Identifying Potential Bottlenecks](#identifying-potential-bottlenecks)
  - [Double Booking](#double-booking)
  - [Database Contention](#database-contention)
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

- Log in with email and password.
- Search for a restaurant.
- Make a reservation.
- Choose the reservation date and time.
- Specify the reservation length.
- Specify the party size.
- Specify dietary restrictions.
- Cancel a reservation.
- Choose a payment method.
- Receive an email confirmation.
- Rate a restaurant and submit reviews/feedback.
- Save favorite restaurants.

The restaurant owner should be able to:

- Register a restaurant.
- Upload `images and videos` of the restaurant.
- Receive booking confirmations, with details of schedule, party size, and reservation length.
- Receive online payments.
- Get analytics and feedback from customers.

# Non-Functional Requirements

The system should be:

- Scalable, prioritizing availability and partition tolerance.
- Reliable (fault-tolerant without losing uploads).
- Designed to handle millions of concurrent connections with fast loading times and minimal latency.

# Infrastructure

1. `Scalability`:

- A normalized database design with `PostgreSQL` can be a starting point for an average size app. This avoids complex update operations in a denormalized database.
- As Access Patterns require more complex join operations and performance bottlenecks emerge, a NoSQL database can be a viable alternative for scalability. According to the CAP theorem, prioritizing [availability and partition tolerance](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure%2Fdatabase%2F05_CAP_theorem.md) implies that `Cassandra`, `CouchDB`, or [DynamoDB](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/database/06_technologies/DynamoDB.md) can be used.
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

- Implement a Content Delivery Network (CDN) to store static data (images and videos) on servers geographically closer to end users, `reducing the load` on origin servers and speeding up content delivery.
- Implement a caching mechanism to `reduce latency` by caching the most frequent database queries.

5. `API Gateway:`

- To provide a single entry point for clients to interact with various backend services, handling tasks such as geo-routing, RESTful API definitions, and access control. It also includes the load balancer.

6. `Backend API`:

- The API for client-server communication can be a [RESTful API](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/backend/restfull_api.md) implemented with [AWS API Gateway](https://aws.amazon.com/api-gateway/)+[Lambda](https://aws.amazon.com/lambda/).

- As fetching becomes complex and performance bottlenecks emerge, then a [GraphQL API](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/backend/grahql.md), implemented with [AppSync](https://aws.amazon.com/appsync/)+[Amplify](https://aws.amazon.com/amplify/) or [graphql-http](https://graphql.org/blog/2022-11-07-graphql-http/), can be a viable alternative.

7. `Deployment`:

- AWS Beanstalk for serverless deployment of long-running containerized or non-containerized applications.

- If more control over the infrastructure is required, use Docker to containerize the Express or Apollo server for ECS (Elastic Container Service) or EKS (Elastic Kubernetes Service), and deploy it using AWS Fargate.

# Services

1. `Frontend Service:` (UX/UI) can be implemented with React.js.

2. `Security Service`: login, authentication, and authorization can be implemented with [AWS Cognito](https://aws.amazon.com/pm/cognito/) and [AWS IAM](https://aws.amazon.com/iam/), respectively. Cognito automatically handles the storage of user credentials (e.g., passwords, tokens) and metadata (e.g., email, phone number, username) inside **Cognito User Pools**.

3. `Storage Service`: [AWS S3](https://aws.amazon.com/s3/) can be used to store compressed image, video, and other binary data with auto replication.

4. `Database Service`: PostgreSQL or DynamoDB can be used to store user preferences/settings, bookings, reviews, favourites, and real-time interactions.

5. `Media Service`: for video upload and pre-processing (encoding and transcoding), a message queue (AWS SQS) can be used to handle encoding and transcoding requests and to send videos to a domain-specific service (possibly a fleet of servers). Post-processed videos are then stored in AWS S3. Available AWS services are:

   - [AWS Elemental MediaLive](https://aws.amazon.com/medialive/): real-time encoder used to convert a raw live video file into a compressed digital format. It does not deliver content to end users.
   - [Amazon Elastic Transcoder](https://aws.amazon.com/elastictranscoder/): basic audio/video transcoding service used to convert an already encoded video from one format to another.
   - [AWS Elemental MediaConvert](https://aws.amazon.com/mediaconvert/): professional-grade transcoder used to convert image and video files in AVC (H.264 or MPEG-4 Part 10) media format.
   - Custom Python functions for data compression can be implemented within AWS Lambda.

6. `Search Engine Service:` the search engine can be implemented with: [Elasticsearch](https://www.elastic.co/elasticsearch), [AWS OpenSearch](https://aws.amazon.com/what-is/opensearch/), and [AWS CloudSearch](https://aws.amazon.com/cloudsearch/).

7. `URL Sharing Service:` users can share their favorite restaurants using a `URL shortener service`.

8. `CDN Service`: takes static data from [AWS S3 Storage](https://aws.amazon.com/s3/). Technologies: [AWS CloudFront](https://aws.amazon.com/cloudfront/)

9. `Caching Service`: [Amazon Elasticache](https://aws.amazon.com/pm/elasticache/) (general-purpose), or [AWS DAX](https://aws.amazon.com/dynamodb/dax/) (purpose-built for DynamoDB). This caching solution stays in the AWS cloud.

10. `Payments Service:` payment transactions can be processed with `Strype` or `PayPal`.

11. `Message Queuing:` [AWS SQS](https://aws.amazon.com/sqs/) can be used to allow asynchronous communication between microservices such as reservation requests, payment processing, and notification dispatching.

12. `Email Confirmation Service:` after a reservation is successfully created, an email confirmation template with relevant reservation details can be triggered via an [AWS Lambda function](https://aws.amazon.com/lambda/). Customers and restaurant owners can then receive the email from an SMTP server (e.g. using `Nodemailer`) or through an email service provider's API (e.g., [Amazon SES API](https://docs.aws.amazon.com/ses/latest/dg/send-email-api.html) or [SendGrid]().

13. `Push Notification Service:` [AWS SNS](https://aws.amazon.com/sns/) can be used to notify users about booking confirmations via SMS or notify services about state changes (e.g. a reservation update).

14. `Metrics and Analytics Service`: DynamoDB does not support analytical operations out of the box. One solution is to combine services such as [DynamoDB Streams](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html) and [Amazon Kinesis Data Analytics](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/what-is.html) to process and aggregate data in real-time. A custom Lambda function can be set up to be triggered whenever data changes in a specific DynamoDB table to be analyzed. [Amazon QuickSight](https://docs.aws.amazon.com/managedservices/latest/userguide/quicksight.html) can be used to create dashboards for analytics, data visualization, and reporting.

15. `Serverless Orchestration Workflow`: [AWS Step Functions](https://aws.amazon.com/step-functions/) can be used to manage bookings, notifying the restaurant, sending customer reminders, or handling cancellations.

# Architecture

1. A monolithic architecture can be a starting point for prototyping and product validation.

2. As the system scales and mature, [microservices architecture with Saga pattern](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/10_patterns.md) is the way to go for isolation and data consistency in a distributed system/application where business transactions span multiple microservices. The SAGA workflow can be implemented serverless with `AWS API Gateway`, `AWS Step Functions`, `AWS Lambda`, and `Amazon DynamoDB`.

## AWS Step Functions

In the workflow of a [database-per-service pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-data-persistence/database-per-service.html) (a single database for each microservice), using a DynamoDB table for Restaurants and another for Payments, the following [AWS Step Functions](https://aws.amazon.com/step-functions/) can be used for serverless orchestration:

1. Book a restaurant: inserts a record into the DynamoDB Restaurants table with a `transactionStatus` of `pending`.

2. Confirm a restaurant: updates the `transactionStatus` attribute in the DynamoDB Restaurants table to `confirmed`.

3. Cancel a reservation: deletes a reservation record from the DynamoDB Restaurants table.

4. Process a payment: inserts a payment record into the DynamoDB Payments table.

5. Cancel a payment: deletes a payment record from the DynamoDB Payments table.

Reference: https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/implement-the-serverless-saga-pattern-by-using-aws-step-functions.html

# Estimations

## Traffic

Suppose the system has 500k DAUs, and each user makes 10 requests for a particular location per day, on average.

- Search requests per day:

$$
500k \space (users) \times 10 \space (searches) = 5M \space searches/day
$$

- Number of Requests Per Second (RPS):

$$
\frac{5M}{(24 \times 3600 \space seconds)} \sim 58 \space RPS
$$

## Storage

Suppose that 10% of DAU are restaurant owners sharing 5 photos of 1MB per day (0.005GB/user), on average.

- Daily storage usage for 50k DAU = 50k users \* 0.005GB/user = 250 GB/day.
- Monthly storage usage = 250 GB/day \* 30 days = 7,5 TB/month.

## Bandwidth

The Bandwidth (data transfer) considering an ingress of 250 GB of data stored per day:

$$
\frac{250 \space GB}{(24 \times 3600 \space seconds)} \sim 2.9 \space MB/second
$$

# Identifying Potential Bottlenecks

## Double Booking

To avoid double booking, an idempotency key (a.k.a unique key) can be used. In the case of a restaurant booking system, this can be a reservation key (`reservation_id`) generated by the backend during checkout. The client then sends this key to a POST API endpoint that will insert a reservation status in the database.

## Database Contention

Two customers competing for the same restaurant reservation slot is an example of [database contention](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/database/08_contention_vs_thundering.md).

To prevent database contention, the following strategies can be implemented: `query optimization with indexing (for relational databases)`, `database denormalization`, and concurrency control via `Locking and serialization`.

# Normalized Database Design

Unlike a microservice architecture, where services are decoupled and independently deployed, a monolithic architecture consolidates all business logic within a single application/codebase deployed as a unit.

Within a monolithic architecture, it is common to have a single relational database for the entire application, which might include multiple tables that interact through JOIN operations.

## Entity-Relationship Diagram (ERD)

...

# Entity Chart

- In a relational database, each entity is represented by a table.
- In a NoSQL database, entities are the nouns of the application.

A minimal version of the system has the following entity chart:

| Entity      | Primary Key: PK   | Sort Key: SK      |
| ----------- | ----------------- | ----------------- |
| User        | USER#username     | USER#username     |
| Restaurants | REST#RestaurantID | REST#RestaurantID |
| Bookings    | USER#username     | BOOK#BookingID    |
| Payments    | USER#username     | PAY#PaymentID     |
| Reviews     | REST#RestaurantID | REV#ReviewID      |
| Location    | LOC#LocationID    | REST#RestaurantID |

## Single Relational Database Design

1. Users Table:

   - Primary Key: userID (uuid)
   - name (varchar)
   - email (varchar)
   - phoneNumber (varchar)
   - favouriteRestaurants (JSON)
   - role (varchar) - customer or restaurant owner
   - createdAt (timestamp)

2. Restaurants Table:

   - Primary Key: restaurantID (uuid)
   - userID (uuid) - Foreign Key for owner's id referencing Users table
   - name (varchar)
   - description (varchar)
   - address (varchar or point)
   - ContactInfo (JSON)
   - CuisineType (varchar)
   - OpeningHours (varchar)
   - availableTables (JSON) - available tables and seats per table
   - availableCreditCards (JSON)
   - imageURL (varchar or JSON) - JSON array for storing image URLs
   - videoURL (varchar or JSON) - JSON array for storing video URLs

3. Bookings Table:

   - Primary Key: bookingID (uuid)
   - userID (uuid) - Foreign Key referencing Users table
   - restaurantID (uuid) - Foreign Key referencing Restaurants table
   - reservationDate (timestamp)
   - partySize (int)
   - reservationLength (int)
   - dietaryRestrictions (varchar)
   - transactionStatus (enum) - Pending/Confirmed/Cancelled

4. Payments Table:

   - Primary Key: paymentID (uuid)
   - bookingID (uuid) - Foreign Key referencing Bookings table
   - transactionID (uuid)
   - paymentMethod (enum) - Visa, Mastercard, PayPal, etc.
   - amount (float)
   - transactionStatus (enum) - Completed/Denied
   - paymentDate (timestamp)

5. Reviews Table:

   - Primary Key: reviewID (uuid)
   - userID (uuid) - Foreign Key referencing the Users table
   - restaurantID (uuid) - Foreign Key referencing Restaurants table
   - rating (int)
   - feedback (varchar)
   - reviewDate (timestamp)

# Denormalized Database Design

In a microservice architecture, one should prioritize the [database-per-service-pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-data-persistence/database-per-service.html).

Within this architecture, it is common to use a denormalized single table design for each individual microservice.

## DynamoDB [Access (Query) Patterns](https://docs.aws.amazon.com/prescriptive-guidance/latest/dynamodb-data-modeling/step3.html)

Access patterns are required to be known before modeling DynamoDB Tables. Recall that when there is more than one entity (item) and a `fetch many` access pattern, a composite primary key should be used to satisfy the required access pattern.

1. Register a User unique on both username and email address (**not required when using Amazon Cognito**):
   - Partition Key (PK): `USER#username`
   - Sort key (SK): `USER#username`
   - Attributes: `username`, `email`, `name`, `role`, etc.
   - Post: use `TransactWriteItems` to create an item with `PK` and `SK` both as `USER#username`.
2. Register a Restaurant (unique on ID):
   - Partition Key (PK): `REST#RestaurantID`
   - Sort key (SK): `REST#RestaurantID`
   - Attributes: `restaurantName`, `address`, `cuisineType`, etc.
   - Post: use `PutItem` with `PK` and `SK` both as `REST#RestaurantID`.
3. Book a Restaurant:
   - Partition Key (PK): `USER#username`
   - Sort key (SK): `#BOOK#BookingID` (KSUID)
   - Attributes: `restaurantID`, `reservationDate`, `reservationLength`, `partySize`, `dietaryRestrictions`, `transactionStatus`.
   - Post: use `TransactWriteItems` with `PK` as `USER#username` and `SK` as `BOOK#BookingID`.
4. Update a Booking:
   - Partition Key (PK): `USER#username`
   - Sort key (SK): `BOOK#BookingID`
   - Put: use `UpdateItem` with `PK` as `USER#username` and `SK` as `BOOK#BookingID`.
5. Fetch the most recent Bookings for a particular User:
   - Partition Key (PK): `USER#username`
   - Sort key (SK): `BOOK#BookingID`
   - Attributes: `restaurantID`, `reservationDate`, `partySize`, `dietaryRestrictions`, `transactionStatus`.
   - Get: use `Query` with `ScanIndexForward=False`, `PK` as `USER#username` and a sort key condition starting with `BOOK#`.
6. Fetch all Restaurants from a particular User:
   - Partition Key (PK): `USER#username`
   - Sort key (SK): `REST#RestaurantID`
   - Attributes: `restaurantName`, `address`, `cuisineType`, `ownerEmail`, etc.
   - Get: use `Query` with `PK` as `USER#username` and a sort key condition starting with `REST#`.
7. Fetch all Restaurants from a particular Location:

   - Partition Key (PK): `LOC#LocationID`
   - Sort key (SK): `REST#RestaurantID`
   - Attributes: `restaurantID`, `restaurantName`, `address`, `cuisineType`, `ownerEmail`, etc.
   - Get: use `Query` with `PK` as `LOC#LocationID` and a sort key condition starting with `REST#`.

8. Fetch all Payments from a particular User:
   - Partition Key (PK): `USER#username`
   - Sort key (SK): `PAY#PaymentID`
   - Attributes: `date`, `amount`, `transactionStatus`, `bookingID`, etc.
   - Get: use `Query` with `PK` as `USER#username` and a sort key condition starting with `PAY#`.
9. Fetch all Reviews from a particular Restaurant:
   - Partition Key (PK): `REST#RestaurantID`
   - Sort key (SK): `REV#ReviewID`
   - Attributes: `Username`, `Rating`, `Feedback`, etc.
   - Get: use `Query` with `PK` as `REST#RestaurantID` and a sort key condition starting with `REV#`.

## DynamoDB Single Table Design

- It is **not** a best practice to use a single database design where all microservices share the same table (a.k.a hot table in this case). This will be shown here for simplicity.

- As discussed in the DynamoDB section, contrary to relational databases, DynamoDB does not support join operations by design. While not recommended, it is possible to simulate/fake a join by performing multiple key-value lookups. For example, one might first fetch a record and then make additional requests to retrieve related records. This pattern should be avoided as much as possible since `network I/O introduces latency and multiple queries increase computational (CPU) cost`.

- With Moore's law closing to an end, CPU operations will end up being more expensive than memory storage on hard drives. Therefore, data redundancy introduced in a single-table design is justifiable as the system scales. The design philosophy of DynamoDB emphasizes a `single-table model through data denormalization to avoid the need for multiple queries`. By using a composite primary key and a single-table design, one can effectively "join" related data without performing multiple queries.

- Recall that access patterns such as `fetch user and bookings from user` (one-to-many relationship) require a `pre-join`, i.e., the Booking items should belong to the same partition key (same item collection) as the parent item (e.g., USER#username). This allows for fetching Users and Bookings in a single request. The same applies to fetching restaurants from locations, etc.

- In the following table, PK and SK are used as generic names to represent the partition key and sort key, respectively. This is called `primary key overloading`. Also, a prefix (e.g., USER#, REST#, REV#, PAY#) is added to each value of a partition key and sort key to avoid overlapping between items with the same name (e.g., a user and a restaurant with the same name), since a primary key must be unique across items. And the prefix `#` in #BOOK# is used to ensure that bookings are fetched in descending order.

<table>
  <tr> 
    <td colspan="2"> Primary Key </td>
    <td colspan="6">Attributes</td>
  </tr>
    <th>Partition Key: PK</th>
    <th>Sort Key: SK</th>
    <th>Attribute 1</th>
    <th>Attribute 2</th>
    <th>Attribute 3</th>
    <th>Attribute 4</th>
    <th>Attribute 5</th>
    <th>Attribute 6</th>
  </tr>
  <tr>  
  </tr>	
  <tr>
    <td rowspan="4">Item Collection<br>USER#sherlock</td>
    <td colspan="1">  </td>
    <td>restaurantName</td>
    <td>address</td>
    <td>cuisineType</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>REST#221</td>
    <td>The Mazarin Stone</td>
    <td>{Street: 221B Baker}</td>
    <td>"Vegan" </td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td colspan="1">  </td>
    <td> Username </td>
    <td> Email </td>
    <td> Name </td>
    <td> Role </td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>USER#sherlock</td>
    <td>sherlock</td>
    <td>sherlock@example.com</td>
    <td>Sherlock Holmes</td>
    <td>Restaurant Owner</td>
    <td></td>
    <td></td>
  </tr>

  <tr>
    <td rowspan="6">Item Collection<br>USER#jamesbond</td>
    <td colspan="1">  </td>
    <td>restaurantName</td>
    <td>address</td>
    <td>cuisineType</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>REST#007</td>
    <td>Goldfinger</td>
    <td>{Street: 007B Square}</td>
    <td>"English" </td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td colspan="1">  </td>
    <td> restaurantID </td>
    <td> reservationDate </td>
    <td> reservationLength </td>
    <td> partySize </td>
    <td> dietaryRestrictions </td>
    <td> transactionStatus </td>
  </tr>
  <tr>
    <td>#BOOK#001</td>
    <td>001</td>
    <td>2024-06-01T10:00:00Z</td>
    <td>"1 hour"</td>
    <td>"Table for 2"</td>
    <td> "Peanut Allergy" </td>
    <td>"Pending"</td>
  </tr>
  <tr>
    <td colspan="1">  </td>
    <td> Username </td>
    <td> Email </td>
    <td> Name </td>
    <td> Role </td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>USER#jamesbond</td>
    <td>jamesbond</td>
    <td>jamesbond@example.com</td>
    <td>James Bond</td>
    <td>Restaurant Owner</td>
    <td></td>
    <td></td>
  </tr>
  
  <tr>
    <td rowspan="4">Item Collection<br>LOC#LDN</td>
    <td colspan="1">  </td>
    <td>restaurantID</td>
    <td>restaurantName</td>
    <td>address</td>
    <td>cuisineType</td>
    <td>ownerEmail</td>
    <td></td>
  </tr>
  <tr>
    <td>REST#221</td>
    <td>221</td>
    <td>The Mazarin Stone</td>
    <td>{Street: 221B Baker}</td>
    <td>"Vegan" </td>
    <td>sherlock@example.com</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="1">  </td>
    <td>restaurantID</td>
    <td>restaurantName</td>
    <td>address</td>
    <td>cuisineType</td>
    <td>ownerEmail</td>
    <td></td>
  </tr>
  <tr>
    <td>REST#007</td>
    <td>007</td>
    <td>Goldfinger</td>
    <td>{Street: 007B Square}</td>
    <td>"English" </td>
    <td>jamesbond@example.com</td>
    <td></td>
  </tr>
 
  <tr>
    <td rowspan="2">USER#jamesbond</td>
    <td colspan="1">  </td>
    <td>date</td>
    <td>amount</td>
    <td>transactionStatus</td>
    <td>bookingID</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>PAY#001</td>
    <td>2024-06-01T10:00:00Z</td>
    <td>50</td>
    <td>Completed</td>
    <td>001</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td rowspan="2">REST#221</td>
    <td colspan="1">  </td>
    <td> Username </td>
    <td> Rating </td>
    <td> Feedback </td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>REV#001</td>
    <td>jamesbond</td>
    <td>5</td>
    <td>"Superb!"</td>
    <td></td>
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

## AWS Pricing

Try the [AWS Calculator](https://calculator.aws/#/addService) to get a quotation for each AWS service used.
