<div align="center">
  <h1> JWT and Refresh Tokens </h1>
</div>

# Table of Contents

- JWT
- Refresh Tokens
- Connection with REST APIs
- Connection with AWS Cognito and IAM

# JWT

A JSON Web Token (JWT) is a compact, URL-safe way of representing claims to be transferred between two parties. It is commonly used for stateless authentication (identity) and authorization (permission) in web applications.

- A JWT contains three parts: 
  - `Header`: the header typically consists of two parts.
    - The type of token (which is always JWT).
    - The signing algorithm being used (e.g., HS256 or RS256).
  - `Payload`: the payload contains the claims, which are statements about an entity (typically, the user) and additional metadata. It could be an **ID Token Payload** or an **Access Token Payload**.
  - `Signature`: ensures the token's integrity and authenticity.

# Refresh Tokens

A refresh token is a long-lived token issued alongside a short-lived JWT (access token).

- **Purpose:** while JWTs are used to access protected resources (e.g. APIs), refresh tokens are used to obtain new JWTs when the old ones expire. This ensures continuous access without requiring users to re-enter their credentials frequently.

- **Lifespan:** refresh tokens typically have a longer lifespan than access tokens.

- **Usage Flow:**
    1. When a user logs in, both a short-lived JWT and a long-lived refresh token are issued.
    2. The client uses the JWT to access resources until it expires.
    3. Once the JWT expires, the client sends the refresh token to the authentication server.
    4. The server validates the refresh token and, if valid, issues a new JWT (and optionally a new refresh token).

# Connection with REST APIs

- In a REST API, JWTs are used for stateless authentication. The server does not store session data, instead, the JWT contains all the necessary information (claims) to verify the user's identity and permissions.

- A client includes the JWT in the Authorization header of each request to access secured endpoints.

- When the JWT expires, the client can send the refresh token to a designated endpoint to obtain a new JWT.

# Connection with AWS Cognito and IAM

- When a user signs in through AWS Cognito, it issues three tokens: the ID token, the access token, and the refresh token.
  - The ID token contains information about the authenticated user (e.g., username, email) and is used for client-side operations.
  - The access token is used to authorize access to protected APIs and AWS services. It includes the scope (e.g., read and write) and the group (e.g., admin).
  - The refresh token is used to obtain a new JWT.

- IAM policies can be defined to restrict access to AWS resources based on claims in the JWT (specifically, the access token).