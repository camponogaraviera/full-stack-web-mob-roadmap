<div align='center'>
    <h1> Monolithic vs Microservices </h1>
</div>

# Table of Contents

- [Monolithic](#monolithic)
- [Microservices](#microservices)

# Monolithic 

A monolithic architecture consolidates all business logic within a single application/codebase.

The key aspect of a monolithic system is that it is deployed as a single unit, regardless of how many databases it interacts with.

Within a monolithic architecture, it is common to have a single relational database for the entire application, which might include multiple tables that interact through JOIN operations.

Note: the business logic in a monolithic architecture can be organized into different modules or components (which might feel like independent services), but they are still part of the same deployed unit.

Pros:

- Easier to manage and deploy initially.
- Simpler development process as everything is in one place.
- Performance can be better due to fewer network hops.

Cons:

- Single point of failure: if one part fails, the whole application can be affected.
- Difficult to scale: the entire application must be scaled even if only one part is experiencing high load.
- Harder to maintain and update over time as the codebase grows.

# Microservices 

Microservices is fundamentally an architectural pattern implemented on the backend of the application. Each service is decoupled (isolated) and self-contained with its own data persistence layer, which often translates to a [database-per-service pattern](https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-data-persistence/database-per-service.html).

Pros:

- Easy to scale: decoupling allows for horizontal scalability, as individual services can be scaled independently based on demand.
- Resilience: if one service fails, others can continue to function.
- Flexibility: different technologies and languages can be used for different services.
- Easier to maintain and update specific parts without affecting the whole system.

Cons:

- More complex to manage and deploy due to the need for inter-service communication and coordination.
- Increased operational overhead: requires robust monitoring, logging, and automated deployment strategies.
- Potential for increased latency due to network calls between services.
- Data consistency can be more challenging to maintain. Can be overcome with Saga Pattern.
