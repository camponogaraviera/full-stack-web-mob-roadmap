<div align='center'>
  <h1> WebSockets </h1>
</div>

# Table of Contents

- [Sockets vs Polling](#sockets-vs-polling)
- [WebSockets](#websockets)
  - [Applications of WebSockets](#applications-of-websockets)

# Sockets vs Polling

- Socket is a communication endpoint that enables real-time, low-latency communication between a client and a server without the need for periodically HTTP requests. When using WebSockets, once the connection is established, either side (client or server) can send data at any time.

- Polling is a technique where the client periodically sends HTTP requests (post, put, get, delete) to the server in one way (client to server). This makes Polling to be more resource-intensive than websockets due to repeated requests. There are two main types:
  - Regular Polling: the client sends a request at fixed intervals (e.g., every few seconds) in a loop.
  - Long Polling: the client sends a request and the server holds the connection open until there is new data. This reduces the number of requests but can still be less efficient than sockets for real-time applications.
  
- Example of Polling:
```javascript
const pollRate = 500;

const pollMessages = setInterval(async () => {
  try {
    const response = await fetch('https://api.github.com/users/camponogaraviera'); 
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const messages = await response.json();
    console.log(messages);
  } catch (error) {
    console.error('Error fetching messages:', error);
  }
}, pollRate);

// clearInterval(pollMessages); // To stop polling.
```
  
# WebSockets

WebSocket is a web communication protocol that provides full-duplex (two-way or bidirectional) communication between a client (usually a web browser) and a server over a single TCP connection. Unlike Server-Sent Events (SSE), which are unidirectional, and traditional HTTP communication, which is inherently request-response-based and can be slow due to the overhead of repeatedly establishing connections, WebSockets enable real-time data transfer with minimal latency, making them ideal for applications that require instant updates.

WebSockets are supported by all modern web browsers and many backend frameworks, including Node.js, Python's Tornado, and Java's Spring Framework. In NodeJs, the `socket.io` library enables websocket to be used on both the frontend and backend, and provides [HTTP long-polling as fallback](https://socket.io/docs/v4/how-it-works/) for browsers that do not support the websocket protocol.

## Applications of WebSockets

- Real-time collaboration tools: applications similar to Google Docs can use WebSockets to provide instant feedback and synchronization among multiple users editing or viewing the same document or chat.
- Real-time location tracking to send and receive updates on driver's location (e.g., Uber).
- Real-time chat applications.
- Online Gaming: multiplayer online games can use WebSockets for real-time communication between players and game servers, enabling quick updates on player actions and game state.
- Financial Market Data Feeds: stock trading platforms and financial services can use WebSockets to deliver real-time updates of stock prices, exchange rates, and market events to traders and investors.
- IoT Communication: webSockets provide a lightweight and efficient communication protocol for Internet of Things (IoT) devices that need to send and receive data in real-time.

# References

[1] https://socket.io/

[2] https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API
