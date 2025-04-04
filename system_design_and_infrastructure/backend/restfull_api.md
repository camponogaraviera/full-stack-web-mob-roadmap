<div align='center'>
  <h1>RESTful API</h1>
</div>

# Table of Contents

- About
- Best suited for
- Similarities with GraphQL
- CRUD Operations
- HTTP Response Status Codes
- Route Handler + Middleware
  - Middleware Example
  - Node.js Express Server Example

# About

A RESTful (a.k.a REST) API is a schema-less communication standard that uses HTTP verbs (CRUD operations) to enable communication (structured data transfer) between a client and a server.

# Best suited for

A REST-based API is best suitable for:

1. Small applications that do not involve complex and hierarchical data structures such as trees or graphs.
2. Simple data querying that does not involve multiple SQL-based JOIN operations.

# Similarities with GraphQL

1. Both are stateless: future requests do not depend on data stored from previous requests, i.e., there is no response history between requests. There's no information about which server has served which request, i.e., there is no information about where the request was routed to. Any server can handle any request.
2. Both use a Client-Server model: requests from a single client result in responses from a single server.
3. Both are HTTP-based: use CRUD operations to create, read, update, and delete data.
4. Both use JSON, XML, or HTML data formats to return data from the server to the client.
5. Both support caching.
6. Both are database agnostic, i.e., they can work with `any database structure` and programming language.

# CRUD Operations

CRUD operations are those that can be used to access and manipulate a resource (data or object). There are four such operations:

- Post (Create): used to create a new resource by sending a post request to a specific endpoint.
- Get (Read): used to retrieve a resource (read-only) from the server by sending a get request to a specific endpoint.
- Put (Update): used to update a resource by sending a put request to a specific endpoint.
- Delete (Delete): used to delete a resource by sending a delete request to a specific endpoint.

Other HTTP Verbs:

- HEAD: a GET request lacking a response body.
- PATCH: a partial update.

# [HTTP Response Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

- 1xx informational response (100-199) – the request was received, continuing process.
  - 100: Continue.
  - 101: Switching protocols.
  - 102: Processing.
  - 103: Early Hints.
- 2xx successful (200-299) – the request was successfully received, understood, and accepted.
  - 200: Ok.
  - 201: Created.
  - 202: Accepted.
  - 203: Non-Authoritative Information.
  - 204: No content.
  - 205: Reset Content.
  - 206: Partial Content.
  - 207: Multi-Status.
  - 208: Already Reported.
- 3xx redirection (300-399) – further action needs to be taken to complete the request.
  - 300: Multiple Choices.
  - 301: Moved permanently.
  - 302: Found.
  - 303: See other.
  - 304: Not modified.
  - 305: Use Proxy.
- 4xx client error (400-499) – the request contains bad syntax or cannot be fulfilled.
  - 400: Bad Request.
  - 401: Unauthorized.
  - 402: Payment Required.
  - 403: Forbidden.
  - 404: Not Found.
  - 405: Method Not Allowed.
  - 406: Not Acceptable.
  - 407: Proxy Authentication Required.
  - 408: Request Timeout.
  - 409: Conflict.
  - 410: Gone.
- 5xx server error (500-599) – the server failed to fulfill a valid request.
  - 500: Internal Server Error.
  - 501: Not Implemented.
  - 502: Bad Gateway.
  - 503: Service unavailable.
  - 504: Gateway Timeout.

# Route Handler + Middleware

- A route handler is a callback function responsible for handling requests sent to a specific URL endpoint (route). It receives the request and response objects as arguments. In the context of defining routes in a framework like Express.js, the `app` object's route methods (such as `app.get`, `app.post`, etc.) receive a URL route and the route handler as arguments.

- A middleware is a callback function that takes a request object, a response object, and a `next()` function. It either passes control to another middleware function by calling `next()` or returns a response to the client, terminating the request-response cycle. For example, the argument of app.use(express.json()) returns a third-party middleware function that parses the request body into a JSON object and then passes control to the next middleware or route handler. Every route handler is a middleware function.

## Middleware Example

```javascript
// Middleware function example:
function myMiddleware(req, res, next) {
  console.log("Middleware executed");
  next(); // Pass control to the next middleware or route handler.
}
```

## Node.js Express Server Example

```javascript
// const express = require("express"); // ES5 syntax.
// const Joi = require("joi"); // Joi class for input validation.
import express from "express"; // ES6 syntax.
import Joi from "joi"; // ES6 syntax.

// Create an instance of an Express application:
const app = express();

// The Port is dynamically assigned by the host environment:
const PORT = process.env.PORT || 3000;

// Mock database to store user data:
const users = []; // Array to be populated with one hash table for each user.

// Custom function for input validation:
function validateUser(user) {
  // Use the Joi class to define a schema for input validation:
  const schema = Joi.object({
    name: Joi.string().min(2).required(),
    email: Joi.string().email().required(),
  });

  // Validate the user object against the schema:
  return schema.validate(user);
}

// Use the express.json() middleware to parse request bodies into JSON objects and pass control to the next route handler:
app.use(express.json());

// GET request to display a message at the `/home` endpoint:
app.get("/home", (req, res) => {
  // This is the body of the route handler where the logic is defined.
  // Send a welcome message to the client:
  res.status(200).send("Welcome to the Home Page!");
});

// GET request to retrieve all users:
app.get("/users", (req, res) => {
  // Send all users to the client:
  res.status(200).send(users);
});

// GET request to retrieve a specific user by ID:
app.get("/users/:userId", (req, res) => {
  // Get the userId from the URL parameter:
  const { userId } = req.params;

  // Find the user by Id querying the mock database:
  const user = users.find((user) => user.userId === parseInt(userId)); // Uses JavaScript built-in array method.

  // If the user does not exist, send a JSON response to the client with status code 404 (Not Found):
  if (!user)
    return res.status(404).send(`User with id ${userId} was not found!`);

  // If it does, send a JSON response to the client with status code 200 (OK) and the user metadata:
  res.status(200).json(user);
});

// POST request to add a user to the mock database.
app.post("/users", (req, res) => {
  // Input validation:
  const { error, value } = validateUser(req.body);

  // If the request body does not match the schema, send a JSON response to the client with status code 400 (Bad Request):
  if (error) return res.status(400).send(error.details[0].message);

  // Destructure userId, name, and email from the request body:
  const { name, email } = req.body;

  // Store the new user in the mock database:
  const newUser = { userId: users.length + 1, name, email };
  users.push(newUser);

  // Send a JSON response to the client with status code 201 (Created) and the added user data:
  res.status(201).json({ message: "User added successfully", user: newUser });
});

// PUT request to update a user by ID:
app.put("/users/:userId", (req, res) => {
  // Get the userId from the URL parameter:
  const { userId } = req.params;

  // Find the user by Id querying the mock database:
  const user = users.find((user) => user.userId === parseInt(userId));

  // If the user does not exist, send a JSON response to the client with status code 404 (Not Found):
  if (!user)
    return res.status(404).send(`User with id ${userId} was not found!`);

  // Input validation:
  const { error, value } = validateUser(req.body);

  // If the request body does not match the schema, send a JSON response to the client with status code 400 (Bad Request):
  if (error) return res.status(400).send(error.details[0].message);

  // Destructure name and email from the request body:
  const { name, email } = req.body;

  // Update the user in the mock database:
  user.name = name;
  user.email = email;

  // Send a JSON response to the client with status code 200 (OK) and the updated user data:
  res.status(200).json({ message: "User updated successfully", user });
});

// DELETE request to remove a user by ID:
app.delete("/users/:userId", (req, res) => {
  // Get the userId from the URL parameter:
  const { userId } = req.params;

  // Find the user by Id querying the mock database:
  const user = users.find((user) => user.userId === parseInt(userId));

  // If the user does not exist, send a JSON response to the client with status code 404 (Not Found):
  if (!user)
    return res.status(404).send(`User with id ${userId} was not found!`);

  // Remove the user from the mock database:
  const index = users.indexOf(user);
  users.splice(index, 1);

  // Send a JSON response to the client with status code 200 (OK) and a success message:
  res.status(200).json({ message: "User deleted successfully" });
});

// Start the server and listen for incoming requests on the defined port:
app.listen(PORT, () => {
  // Log a message to the console:
  console.log(
    `Running server in ${process.env.NODE_ENV} mode and listening on http://localhost:${PORT}\n`
  );
});
```

### Testing with Postman

1. Install dependencies and start the development server:

```bash
mkdir test && cd test && npm init --yes && npm install express joi && touch app.js
```

Note: add `"type": "module"` in `package.json` to enable ES6 syntax, or install Babel.

2. Install [postman desktop](https://learning.postman.com/docs/getting-started/installation/installation-and-updates/) to send a POST request:

```bash
snap install postman
```

3. Open postman desktop.
4. Select `POST` from the dropdown menu next to the URL field.
5. Type `http://localhost:3000/users/`.
6. Click on the `Body` tab below the URL field.
7. Since the server is configured to parse JSON bodies, select `raw` and choose `JSON` from the dropdown menu to the right.
8. Finally, send a POST request in a JSON-like format:

```bash
{
    "name": "bob",
    "email": "bob@example.com"
}
```

1. In the browser, go to the endpoint http://localhost:3000/users/1. The display message should be: `{"userId":1,"name":"bob","email":"bob@example.com"}`.
2. Check the network tab in Developer tools (`Ctrl+Shift+I`).
