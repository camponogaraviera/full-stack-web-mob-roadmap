<div align='center'>
  <h1>GraphQL</h1>
</div>

# Table of Contents

- [About GraphQL](#About-GraphQL)
- [Best suited for](#Best-suited-for)
- [Similarities with RESTful APIs](#Similarities-with-RESTful-APIs)
- [Advantages and Disadvantages over RESTful APIs](#Advantages-and-disadvantages-over-RESTful-APIs)
- [CRUD Operations in GraphQL](#CRUD-operations-in-GraphQL)
- [GraphQL Syntax](#GraphQL-Syntax)
- [Implementing a GraphQL Server](#implementing-a-graphql-server)
- [Deployment](#deployment)
 
# About GraphQL

GraphQL is a schema-based API query language that enables client-server communication typically operating over a single endpoint using HTTP. It specifies how the frontend should request structured data from the backend and how the backend should respond to it. The backend then aggregates and returns the requested data, potentially from multiple sources. It uses a single API call operating over a single endpoint using the HTTP protocol (although it is not limited to it). GraphQL is a language-agnostic specification, i.e., any programming language (e.g., C++, Python, Java, JavaScript) can be used to create GraphQL APIs. 

To build scalable systems, GraphQL schemas are often split across multiple files or modules for better organization. In a microservice architecture, [Apollo Federation](https://www.apollographql.com/docs/graphos/schema-design/federated-schemas/federation) is a popular approach to manage and unify multiple APIs into a single federated graph, giving the appearance of a monolithic API. This allows clients to query data seamlessly through a single endpoint, while the backend efficiently resolves queries by delegating calls to the respective microservices.

The communication between the client and the backend can be done through three possible operations:

- `Query:` used for data fetching. Is set by default.
- `Mutation:` used to change the data.
- `Subscription:` for real-time updates. When a certain event occurs on the server (such as data change), the server pushes the update to the client without the need for the client to repeatedly request it.

- Notes:
  - A query language (e.g., SQL, GraphQL) is a tool for requesting or manipulating data.
  - The GraphQL specification describes the rules, structure, and syntax of the GraphQL query language. 
  - The `schema` uses graphQL's own types, instead of TypeScript types, to define how clients should query and manipulate data.

GraphQL can be used in both the frontend and the backend of an application:

- Backend: in the backend of the application, GraphQL is used to define a `schema` that specifies the structure of the data (`types`) and the available operations (`queries, mutations, subscriptions`). Alongside the schema, `resolvers` (functions) are implemented to handle the aforementioned operations such as fetching the data from the appropriate sources and shaping it according to the schema before returning it to the user.
- Frontend: in the frontend, GraphQL can be used alongside [Apollo Client](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/frontend/apollo_vs_redux.md) to handle the interaction between the client-side (frontend) and the server-side (backend). For example, Apollo Client can be used to send GraphQL [queries](https://www.apollographql.com/docs/react/data/queries/) and [mutations](https://www.apollographql.com/docs/react/data/mutations/) to the backend server. 

# Best suited for

A GraphQL-based API is best suitable for:

1. Large, complex, and interrelated (nested) data that need to be fetched with a single API call (request) through a single URL endpoint. For example, fetching a user, their posts, and the comments on those posts.
2. Composite patterns where there is a need to retrieve data from multiple sources (e.g., logging, third-party analytics, etc.).
3. When there is limited bandwidth (e.g., mobile phones and IoT devices) and a need to limit the number of requests and responses.
4. When Client requests and responses are expected to vary.

# Similarities with RESTful APIs

1. Both are stateless: future requests do not depend on data stored from previous requests, i.e., there is no response history between requests. There's no information about which server has served which request, i.e., there is no information about where the request was routed to. Any server can handle any request.
2. Both use a Client-Server model: requests from a single client result in responses from a single server.
3. Both are HTTP-based: use CRUD operations to create, read, update, and delete data.
4. Both use JSON, XML, or HTML data formats to return data from the server to the client.
5. Both support caching.
6. Both are database agnostic, i.e., they can work with `any database structure` and programming language.

# Advantages and Disadvantages over RESTful APIs

Problems in RESTful APIs:

1. Under-fetching: the response returns a whole data item (e.g., person's name, date of birth, address, and phone number) when only requesting for specific data (e.g., phone number).
2. Overfetching: when multiple  round trips (REST API requests) are needed to gather related data (e.g., a user's phone number and last purchase) from different endpoints (/person and /purchase).
3. Requires new endpoints to be created when adding new query features. REST APIs version their endpoints when changes like deprecating a field occur. For example, an older version (`/api.domain.TLD/v1/users`) may be replaced with a newer version (`/api.domain.TLD/v2/users`).

GraphQL solution:

1. GraphQL uses a single API request call to fetch/retrieve the exact data needed and no more.
2. GraphQL uses a single endpoint, and only needs the schema to add a new query. In GraphQL, the endpoint (/graphql by default) remains the same regardless of schema changes, including field deprecations. Fields can be marked as deprecated in the schema using the @deprecated directive, which signals clients to avoid using the field.

GraphQL Disadvantages:

1. GraphQL requires server-side implementation to support GraphQL in the frontend.
2. Caching is complex with GraphQL.

# CRUD Operations in GraphQL

CRUD operations are those that can be used to access and manipulate a resource (data or object). There are four such operations:

- Mutation (Create): used to create a new resource by sending a post request to a specific endpoint.
- Query (Read): used to retrieve a resource (read-only) from the server by sending a query request to a specific endpoint.
- Mutation (Update): used to update a resource by sending a put request to a specific endpoint.
- Mutation (Delete): used to delete a resource by sending a delete request to a specific endpoint.

# GraphQL Syntax 

- Schema: defines the types, queries, mutations, and subscriptions available in the GraphQL API.

- Type: defines the structure and shape of the data that can be query or mutate in the GraphQL API. It is classified into:
  - Scalar types: String, Int, Float, Boolean, and ID.
  - Object types: custom types, such as `Query` that contain fields, each of which has its own types.
  - Enum type: is a special scalar type that allows a field to have one of a specific set of allowed values. 

- Field (a.k.a attribute): represents the actual data that can be queried or mutated within a type defined in the GraphQL schema. Each type in a schema can have one or more fields. 

- Resolver: is a function that determines how to retrieve or compute the value for a particular field, effectively shaping the response according to the schema's definitions. Each field in a schema can have a corresponding resolver.

- Type Modifier: modify the behavior of a specific type.
  - Non-null type: represented by an exclamation mark `!`, means that the server/resolver should always return a non-null value. The resolver should handle a null value from the database and return a default non-null value instead. If the resolver returns a null value, GraphQL will throw an error.
  - List type: means that the server/resolver should return an array of a type defined within a square bracket `[type]`. The list itself can be null unless marked with `!`, and its elements can be null unless the type inside is marked with `!`.

Example of a GraphQL schema:
```javascript
const schema = buildSchema(`
  // Defining a User type that contains four fields where some of them have a type modifier:
  type User {
    id: ID!         # Non-null ID. Must have a non-null value.
    name: String!   # Non-null string. Must have a non-null value.
    email: String!  # Non-null string. Must have a non-null value.
    age: Int!       # Non-null integer. Must have a non-null value.
    friends: [User] # Nullable list of nullable Users. Can be null or contain null elements.
  }

  // Defining a Query type containing the User field:
  type Query {
    user(id: ID!): User
  }
`);
```

Example of a resolver for the above schema:
```javascript

// Mockup data:
const usersData = [
  { id: "1", name: "Alice", email: "alice@example.com", age: 30, friends: ["2", "3"]},
  { id: "2", name: "Bob", email: "bob@example.com", age: 30, friends: ["1", "3"]},
  { id: "3", name: "Charles", email: "charles@example.com", age: 30, friends: ["1", "2"]},
];

const resolvers = {
  user: ({ id }) => {
    return usersData.find(user => user.id === id);
  }
};  
```

Querying a user requesting only the name and email fields:
```javascript
query {
  user(id: "1") {
    name
    email
  }
}
```

# Implementing a GraphQL Server 

The following are available options for building a GraphQL server.

- `express` + [graphql-http](https://graphql.org/blog/2022-11-07-graphql-http/) + `graphql`: to build small GraphQL servers with minimal setup. Allows full control over the GraphQL schema and resolvers.

- `Apollo + GraphQL:` to build scalable, production-ready GraphQL servers with out-of-box features.

- `Amplify + AppSync:` to build scalable GraphQL servers for AWS-integrated or serverless applications. It's a fully managed solution that works well with other AWS services and provides real-time support.
  - `AppSync:` used to simplify the development of GraphQL APIs that can integrate with various AWS services, such as Amazon DynamoDB, AWS Lambda, and more.
  - `Amplify:` used to abstract away the complexity of configuring AWS services and connecting the frontend with the backend (AppSync, and other services).
 
- Install packages:
```bash
yarn add graphql express express-graphql
```

- Create the server:
```javascript
import express from "express"; 
import { graphqlHTTP } from 'express-graphql'; // Middleware.
import { buildSchema } from 'graphql';

// Here, we define a schema with a Query that contains two fields (attributes) named description (of type String) and review (of type Float):
const schema = buildSchema(`
 type Query {
   description: String
   review: Float
 }
`);

// Here, we define two resolvers named `description` and `review, one for each field:
const resolvers = {
  description: () => "This is a sample description.",
  review: () => 4.5
};

// Create an instance of an Express application:
const app = express();

// The Port is dynamically assigned by the host environment:
const PORT = process.env.PORT || 3000;

app.use('/graphql', graphqlHTTP({
  schema: schema,
  rootValue: resolvers,
  graphiql: true,  // Enables GraphiQL UI for testing.
}));

// Start the server and listen for incoming requests on the defined port:
app.listen(PORT, () => {
  // Log a message to the console:
  console.log(
    `Running server in ${process.env.NODE_ENV || "development"} mode and listening on http://localhost:${PORT}/graphql\n`
  );
});
```

- Run the server:
```bash
node server.js
```

- Write a Query to run with GraphiQL:
```bash
{
  description
  review
}
```

- Response should be:
```bash
{
  "data": {
    "description": "This is a sample description.",
    "review": 4.5
  }
}
```

# Deployment 

- Infrastructure and PaaS (Platform as a Service):
  - [Amazon Elastic Compute Cloud (EC2)](https://aws.amazon.com/pt/ec2/): deploy your server on an EC2 instance if you prefer to manage the infrastructure yourself. Containerization is optional.
  - [AWS Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk): use Elastic Beanstalk to simplify infrastructure management and deploy long running containerized or non-containerized applications. AWS manages the underlying EC2 instances, but it is not fully serverless.
- Serverless:
  - [AWS Lambda](https://aws.amazon.com/pm/lambda): use AWS Lambda with [apollo-server-lambda](https://www.npmjs.com/package/apollo-server-lambda) and API Gateway for a fully serverless architecture. Integrate your Express or Apollo server with AWS Lambda using said package. Connect API Gateway to your Lambda function to handle incoming GraphQL requests and route them to your server. Suitable for lightweight applications within Lambda's constraints (15-minute timeout, memory limits) that do not require a long-running service.
  - [AWS Fargate](https://aws.amazon.com/fargate/): use Docker to containerize Express or Apollo servers for ECS (Elastic Container Service) or EKS (Elastic Kubernetes Service), and deploy them using AWS Fargate. There is no need to manage EC2 instances, AWS takes care of the underlying infrastructure (VMs, scaling, etc).
