<div align='center'>
  <h1> CORS </h1>
</div>

# Table of Contents

- About
- How does CORS work?
- Example 1: Setting up CORS in a Node.js Express Server (RESTful API).
- Example 2: Setting up CORS using the Node.js [cors package](https://www.npmjs.com/package/cors).
  
# About

[Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) is a security feature/mechanism implemented by web browsers to restrict cross-origin HTTP requests. 

When an [AJAX](https://en.wikipedia.org/wiki/Ajax_(programming)) request ([XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)) from a particular URL (protocol://domain1:port) is made to a different URL (protocol://domain2:port), the browser enforces CORS policies to ensure that the server allows such request made between different origins. Recall that changing either the protocol, domain or port also changes the [URL](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/infrastructure/01_url_dns.md):
- protocol1://domain1:port1.
- protocol2://domain1:port1 (protocol changed).
- protocol1://domain2:port1 (domain changed).
- protocol1://domain1:port2 (port changed).

Even though CORS is implemented by the browser, it needs to be implemented in the backend of the application by including specific HTTP headers in the response of the RESTful API verb/operation (GET, POST, etc.). When using AWS as the hosting provider, one can enable CORS via AWS Lambda functions. 

# How does CORS work?

Before CORS, AJAX requests could only be made to servers belonging to the same origin (domain) as the webpage/browser making the request. This was known as "same origin restriction". Ansatz solutions for this problem at the time were Proxy or JSONP (limited to only GET requests). CORS emerges as a new standard.

Suppose that your website named `domain1.com` has an image tag `<img>` or a script tag `<script>` to load contents from a website named `domain2.com` via AJAX requests.

The browser will do a pre-flight request, i.e., an OPTIONS request that has the following headers:
- Origin header, which is the domain with the scheme of the webpage making the request.
- Access-Control-Request-Method: the GET, POST, and PUT requests that the browser can make.
- Access-Control-Request-Headers: the headers of the browser.

While the response in the backend of your server may have the following headers:
- Access-Control-Allow-Origin: it could be the origin header, i.e., the URL/domain of the webpage making the request. It could also be `*` denoting that any origin is allowed.
- Access-Control-Allow-Methods: the allowed methods defined in your backend.
- Access-Control-Allow-Headers: the list of allowed headers.
- Access-Control-Max-Age: for how long the response is valid.
- [Access-Control-Allow-Credentials]: http off credentials and cookies.

# Example 1 

Setting up CORS in a Node.js Express Server (RESTful API):

```javascript
const express = require('express');
const app = express();

// Middleware to parse JSON bodies
app.use(express.json());

// Global middleware function to enable CORS:
app.use((req, res, next) => {
  // Allow requests from specific origin:
  res.setHeader('Access-Control-Allow-Origin', 'https://www.my_domain.com');
    
  // Allow certain HTTP methods:
  res.setHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, PATCH, DELETE');

  // Continue to the next middleware:
  next();
});

// RESTful API routes:
app.get('/', (req, res) => {
  // Write the middleware logic here to handle this GET request...
  res.json({ message: 'GET request received' });
});

app.post('/', (req, res) => {
  const postData = req.body;
  console.log('Received data:', postData);
  res.status(200).send('POST request received successfully');
});

// Port is dynamically assigned by the host environment:
const PORT = process.env.PORT || 3000;
// Start the server:
app.listen(PORT, () => {
  console.log(
    `Running server in ${process.env.NODE_ENV} mode and listening on port ${PORT}...\n`
  );
});
```

# Example 2

Setting up CORS using the Node.js [cors package](https://www.npmjs.com/package/cors):

```javascript
const express = require("express");
const cors = require("cors");
const route_handlers = require("./router/route_handlers.js");
const app = express();

// Middleware to parse JSON bodies
app.use(express.json());

// Use CORS middleware (callback function):
const corsOptions = {
  origin: process.env.FRONTEND_DOMAIN || "http://localhost:3000",
  methods: "GET, POST", // Allowed HTTP methods.
  optionsSuccessStatus: 200, // Legacy browsers (IE11, various SmartTVs) might not handle (choke) status 204.
};
app.use(cors(corsOptions));

// RESTful API routes:
app.use("/", route_handlers); // "/" is the URL route, and "route_handlers" is the express router containing a series of route handlers to be executed when a request matches the specified route.

// Port is dynamically assigned by the host environment:
const PORT = process.env.PORT || 3000;
// Start the server:
app.listen(PORT, () => {
  console.log(
    `Running server in ${process.env.NODE_ENV} mode and listening on port ${PORT}...\n`
  );
});
```
