<div align='center'>
    <h1> Patterns for Enabling Data Persistence </h1>
</div>

# Table of Contents

- Saga Pattern
  - Serverless Implementation
- Database-Per-Service Pattern
- Shared-Database-Per-Service Pattern
- API Composition Pattern
- CQRS Pattern
- Event Sourcing Pattern

# Saga Pattern

The [saga pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-data-persistence/saga-pattern.html) is a failure management pattern used to manage long-lived distributed transactions across multiple microservices, ensuring consistency through a series of compensating actions if a failure occurs. It avoids the complexity and overhead of traditional distributed transactions that rely on protocols such as [Two-Phase Commit (2PC)]() and [Three Phrase Commit (3PC)](). Instead of using a single atomic transaction across multiple services, the Saga pattern `breaks down a distributed transaction into a series of smaller, coordinated local transactions that communicate asynchronously`. In this way, each step can either complete successfully or be compensated (reverted to a previous step) if a transaction fails at some step in the workflow, maintaining data consistency. Each step then becomes ACID-compliant at the local level. 


In a [microservices architecture](https://aws.amazon.com/microservices/#:~:text=Microservices), where each service is decoupled (isolated) and self-contained with its own data persistence layer (a `database-per-service pattern`), a single/local ACID transaction cannot span multiple services because each service manages its own database. Attempting to use a local ACID transaction across multiple services can result in partial distributed transactions, where only some operations succeed while others fail, potentially leading to data inconsistencies. The saga pattern is used to prevent these issues to happen.

Transactions can be coordinated in two ways:

1. `Choreography-based Saga:` a local transaction triggers another local transaction. Microservices communicate via events (rather than direct calls) using services such as AWS EventBridge, SQS, and SNS. Other services subscribe (listen) to those events to trigger actions. [AWS AppSync](https://github.com/camponogaraviera/aws/blob/dev/services/aws_api.md) supports [GraphQL](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/backend/grahql.md) resolvers to connect GraphQL operations (queries, mutations, and subscriptions) to services. For example, GraphQL subscriptions can push real-time updates to clients (usually via WebSocket or another transport protocol) when mutations (e.g., data changes and transactions) occur in the backend. While AppSync handles client-facing real-time updates, event-driven services like EventBridge, SNS, or SQS are better suited for backend microservice communication. Use case: a restaurant "Reservation Confirmed" event triggers a payment, then a "Payment Processed" event triggers a notification.
    - [EventBridge](https://github.com/camponogaraviera/aws/blob/dev/services/aws_integration.md#aws-eventbridge) can trigger compensating actions by routing events to appropriate handlers (functions). It is also useful for decoupling microservices, ensuring that failures in one service do not directly impact another.
    -  [SQS](https://github.com/camponogaraviera/aws/blob/dev/services/aws_messaging.md#amazon-sqs) supports retrying failed steps using [dead-letter queues (DLQs)](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html).
    -  [SNS](https://github.com/camponogaraviera/aws/blob/dev/services/aws_messaging.md#amazon-sns) is typically used for broadcasting events from one service to multiple subscribers or services. 

2. `Orchestration-based Saga:` a central orchestrator (e.g., [AWS Step Functions](https://github.com/camponogaraviera/aws/blob/dev/services/aws_integration.md#aws-step-functions)) coordinates the entire workflow, i.e., the sequence of steps in the saga, by calling each microservice and ensuring each step is completed before moving to the next. Step Functions can trigger [AWS Lambda functions](https://github.com/camponogaraviera/aws/blob/dev/services/aws_compute_deployment.md#aws-lambda) directly or interact with [AWS AppSync](https://github.com/camponogaraviera/aws/blob/dev/services/aws_api.md) APIs indirectly (via API Gateway) to run code in response to events (e.g., user registration, payment transactions, DynamoDB table updates, etc.).

- Notes:
  - A local transaction is a sequence of database operations performed as a single unit within a single database. This ensures that all operations are successfully completed (committed) or none of them are (rolled back) if an error occurs, maintaining ACID compliance.
  - A distributed transaction is a sequence of coordinated database operations that span (are performed on) multiple databases, often located across different servers. These transactions can be coordinated with protocols such as Two-Phase Commit (2PC) and Three Phrase Commit (3PC).
  - A step is a single unit of work within a distributed transaction. Each step in the saga is typically a call to a service (a Lambda function or another AWS service) that performs a specific action, optionally with a compensating transaction if a failure occurs.

## Examples 

Use a SAGA pattern:

1. If you have multiple distributed microservices that must maintain consistency:
   - Reservation service.
   - Payment service (e.g., Stripe).
   - Calendar service (e.g., Google Calendar integration).
   - Notification service.

2. If there are long-lived transactions and you don't want microservices to be blocked when a microservice runs for a long time.
   
3. If you need complex rollback operations in case they fail:
   - Cancel a reservation: if an error occurred or the user decided.
   - Refund payments: if a reservation was canceled, the payment should be reversed.
   - Release calendar slots: if payment or another step fails, the reserved time slot should be freed.
   - Notify multiple parties: ensure users and service providers are informed about any changes (success or failure).

## Serverless Implementation

The saga workflow can be implemented using serverless components on AWS. `AWS API Gateway`, `AWS Step Functions`, `AWS Lambda`, and `Amazon DynamoDB` can be orchestrated to implement the saga pattern.

Use case: [Implement the serverless saga pattern by using AWS Step Functions](https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/implement-the-serverless-saga-pattern-by-using-aws-step-functions.html).

# Database-Per-Service Pattern

Using the database-per-service pattern in a microservices architecture ensures that each service can manage its own database independently while still participating in a coordinated workflow. This allows for better isolation and scalability. 

In [this example](https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-data-persistence/database-per-service.html), a system with three microservices (sales, costumer, compliance) uses one dedicated database for each service to align with the database-per-service pattern. There are no restrictions on the choice of the database, different microservices can use different databases (SQL or NoSQL) depending on the service's requirement. In DynamoDB, tables are independent without relying on JOIN operations or foreign keys, which fits the database-per-service model.

# Shared-Database-Per-Service Pattern

In this pattern, multiple services share a common database instance, even if they have separate tables within that database. In an RDS, multiple tables within a single database instance share infrastructure and relational features such as JOIN operations and foreign keys, fitting the shared-database-per-service model.

In [this example](https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-data-persistence/shared-database.html), having three tables (sales, customer, and compliance) within a single RDS database aligns with the shared-database-per-service pattern. While each service may have its own table, they all share the same underlying database instance.

# API Composition Pattern

# CQRS Pattern

# Event Sourcing Pattern
