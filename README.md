[![Contributions](https://img.shields.io/badge/contributions-welcome-orange?style=flat-square)](https://github.com/camponogaraviera/full-stack-web-mob-roadmap/pulls)
[![Maintenance](https://img.shields.io/badge/Maintained%3F-yes-green.svg)](https://github.com/camponogaraviera/full-stack-web-mob-roadmap/graphs/commit-activity)

<div align='center'>
  <h1> Full Stack Web & Mobile Development Roadmap </h1>
</div>

# Table of Contents

## 1. JavaScript

- [Fundamentals of Modern JavaScript (ES6+)](https://github.com/camponogaraviera/javascript)

## 2. [Data Structures & Algorithms](https://github.com/camponogaraviera/ds-and-algo)

- [Theory](https://github.com/camponogaraviera/ds-and-algo/blob/dev/theory/README.md)
- [Quiz](https://github.com/camponogaraviera/ds-and-algo/blob/dev/Quiz/data_structures.md)
- Implementations:
  - [JavaScript](https://github.com/camponogaraviera/ds-and-algo/blob/dev/javascript/README.md)
  - [Python](https://github.com/camponogaraviera/ds-and-algo/blob/dev/python/README.md)
  - [C++](https://github.com/camponogaraviera/ds-and-algo/blob/dev/cpp/README.md)
- [Leetcode](https://github.com/camponogaraviera/ds-and-algo/blob/dev/leetcode/README.md)

## 3. System Design

### 3.1 [URL/DNS/SSL](system_design_and_infrastructure/01_url_dns.md)

- URL
- DNS
- SSL
- Registering a DNS with AWS

### 3.2 [Servers](system_design_and_infrastructure/02_servers.md)

- Stateful vs Stateless
- Cold standby
- Warm standby
- Hot standby

### 3.3 [Over-Provisioning for Resiliency](system_design_and_infrastructure/03_over_provisioning.md)

### 3.4 [Vertical Scaling](system_design_and_infrastructure/04_vertical_scaling.md)

### 3.5 [Sharding and Horizontal Scaling](system_design_and_infrastructure/05_horizontal_scaling.md)

- Sharding
- Horizontal Scaling
  - Load Balancer
  - Routing Algorithms
    - Round robin
    - Weighted round-robin
    - Least Connections
    - Weighted Least Connections
    - IP Hash
    - Least Response Time
    - Random
    - Least Bandwidth
  - Consistent Hashing
  - Database Replication
  - Multi-master Replication
  - Bidirectional and Circular Replication

### 3.6 [Prefetching and Caching](system_design_and_infrastructure/06_prefetching_and_caching.md)

- Prefetching
- Caching
  - Expiration policy
  - The cold-start problem in massive-scale systems
  - Eviction Cache Policies
  - Caching Technologies

### 3.7 [CDN](system_design_and_infrastructure/07_cdn.md)

### 3.8 [The Hot Spot (Celebrity) Problem](system_design_and_infrastructure/08_celebrity.md)

### 3.9 [Monolithic vs Microservices](system_design_and_infrastructure/09_mono_vs_micro.md)

### 3.10 [Patterns for Enabling Data Persistence](system_design_and_infrastructure/10_patterns.md)

- Saga Pattern
  - Serverless Implementation
- Database-Per-Service Pattern
- Shared-Database-Per-Service Pattern
- API Composition Pattern
- CQRS Pattern
- Event Sourcing Pattern

### 3.11 [Software Development Life Cycle (SDLC)](system_design_and_infrastructure/11_sldc.md)

- About
- Waterfall Model
- Iterative Model
- Spiral Model
- Agile Model
- V-Model (Validation and Verification Model)
- Rapid Application Development (RAD) Model
- Big Bang Model
- Incremental Model
- DevOps Model

### 3.12 Database

- [OLAP vs OLTP](system_design_and_infrastructure/database/00_olap_vs_oltp.md)
- [Data Lake vs Data Warehouse](system_design_and_infrastructure/database/01_lake_vs_warehouse.md)
- [Relational Databases](system_design_and_infrastructure/database/02_relational_db.md)
- [Non-Relational (NoSQL) Databases](system_design_and_infrastructure/database/03_non_relational_db.md)
- [Vector Databases](system_design_and_infrastructure/database/04_vector_db.md)
- [ACID and BASE Properties](system_design_and_infrastructure/database/05_ACID_BASE.md)
- [CAP Theorem a.k.a Brewer's Theorem](system_design_and_infrastructure/database/06_CAP_theorem.md)
- [Data Normalization vs Denormalization](system_design_and_infrastructure/database/07_norm_denorm.md)
- [Geo-spatial Indexes](system_design_and_infrastructure/database/08_geo_spatial_indexes.md)
  - Geohashing
  - Quadtrees
- [Database Contention, Thundering Herd, & Deadlock](system_design_and_infrastructure/database/09_contention_vs_thundering.md)
- Technologies
  - [DynamoDB](system_design_and_infrastructure/database/10_technologies/DynamoDB.md)
  - [MongoDB](system_design_and_infrastructure/database/10_technologies/MongoDB.md)
  - [MongoDB VS DynamoDB](system_design_and_infrastructure/database/10_technologies/mongoVSdynamo.md)
  - [Neo4j](system_design_and_infrastructure/database/10_technologies/Neo4j.md)

## 4. Frontend

- [ReactJS](system_design_and_infrastructure/frontend/reactjs.md)
- [React Native](https://github.com/camponogaraviera/react-native)
- [Apollo vs Redux](system_design_and_infrastructure/frontend/apollo_vs_redux.md)
- [TypeScript](system_design_and_infrastructure/frontend/typescript.md)
- [Next.js](system_design_and_infrastructure/frontend/nextjs.md)

## 5. Backend

- [Node.js vs Deno](system_design_and_infrastructure/backend/nodejs_vs_deno.md)
- [RESTful API](system_design_and_infrastructure/backend/restfull_api.md)
- [JWT and Refresh Tokens](system_design_and_infrastructure/backend/jwt_and_refresh_tokens.md)
- [GraphQL](system_design_and_infrastructure/backend/grahql.md)
- [gRPC](system_design_and_infrastructure/backend/gRPC.md)
- [CORS](system_design_and_infrastructure/backend/cors.md)
- [WebSockets](system_design_and_infrastructure/backend/web_sockets.md)
  - Industry Case: [How WebSockets cost us $1M on our AWS bill](https://www.recall.ai/post/how-websockets-cost-us-1m-on-our-aws-bill)
- [ORM & ODM](system_design_and_infrastructure/backend/orm_odm.md)
- [SST](system_design_and_infrastructure/backend/sst.md)

## 6. Tooling & Version Control

- Version Control:
  - [SemVer](https://github.com/camponogaraviera/nvm-npm-yarn?tab=readme-ov-file#semantic-versioning-semver)
  - [Git](https://github.com/camponogaraviera/linux-git-conda/blob/dev/github_essentials/README.md)

- Environment Management:
  - [Conda](https://github.com/camponogaraviera/linux-git-conda/blob/dev/conda_essentials/README.md)
  - [NVM](https://github.com/camponogaraviera/nvm-npm-yarn?tab=readme-ov-file#node-version-manager-nvm)

- Package Management:
  - [NPM](https://github.com/camponogaraviera/nvm-npm-yarn?tab=readme-ov-file#node-package-manager-npm)
  - [Yarn](https://github.com/camponogaraviera/nvm-npm-yarn?tab=readme-ov-file#yarn-package-manager)

## 7. [AWS](https://github.com/camponogaraviera/aws)

## 8. Mock Design Interviews (DynamoDB + AWS)

- [Design a Restaurant Reservation System](mock_design/rest_reservation.md)
- [Design a YouTube-like On-Demand Video Streaming Service](mock_design/youtube.md)
  - [Deliver live streaming video with CloudFront and AWS Media Services](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/live-streaming.html)
- [Design a Search Engine like Google](mock_design/google_search_engine.md)
- [Design an End-To-End LLM Big Data Pipeline for Batch Training and Real-Time Inference](mock_design/data_pipeline.md)
- [Design a Big Data Lambda Architecture for Batch and Real-Time Analytics](mock_design/lambda_architecture.md)

# Refereces

[1] https://aws.amazon.com/

[2] [Awesome DynamoDB](https://github.com/alexdebrie/awesome-dynamodb)

[3] https://react.dev/

[4] https://reactnative.dev/

[5] https://nodejs.org/en

[6] https://deno.com/

[7] https://graphql.org/

[8] https://www.apollographql.com/docs/

[9] https://grpc.io/

[10] https://socket.io/

[11] https://developer.mozilla.org/en-US/docs
