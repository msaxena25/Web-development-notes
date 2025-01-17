# **Polling and Long Polling Technique**

Polling and long polling are methods to achieve **real-time communication** in web applications, especially when modern techniques like WebSockets are unavailable or unnecessary.

---

### **1. Polling**  
**Description**:  
Polling is a technique where the client repeatedly sends HTTP requests to the server at regular intervals to check for new updates or data.

- **How it works**:
  1. The client sends a request to the server.
  2. The server responds immediately, whether or not there’s new data.
  3. The client waits for a fixed period (e.g., 1 second) before sending the next request.

- **Pros**:
  - Simple to implement.
  - Works with any server or browser.

- **Cons**:
  - Inefficient, as it sends unnecessary requests when no new data exists.
  - Increased server load due to frequent requests.

- **Example**:
  ```javascript
  function pollServer() {
    fetch('/poll')
      .then((response) => response.json())
      .then((data) => {
        console.log('New data:', data);
      })
      .catch((err) => console.error('Error:', err))
      .finally(() => {
        setTimeout(pollServer, 1000); // Poll again after 1 second
      });
  }
  pollServer();
  ```

---

### **2. Long Polling**  
**Description**:  
Long polling is a more efficient variant of polling. The client sends an HTTP request to the server, and the server holds the request open until new data is available or a timeout occurs.

- **How it works**:
  1. The client sends a request to the server.
  2. The server **delays its response** until there’s new data or a specified timeout (e.g., 30 seconds).
  3. Once the client receives the response, it immediately sends another request to keep the communication going.

- **Pros**:
  - Reduces unnecessary requests compared to regular polling.
  - Better suited for applications where data changes infrequently.

- **Cons**:
  - Slightly more complex to implement than regular polling.
  - Still not as efficient as WebSockets for high-frequency updates.

- **Example**:
  ```javascript
  function longPollServer() {
    fetch('/long-poll')
      .then((response) => response.json())
      .then((data) => {
        console.log('New data:', data);
        longPollServer(); // Send another request immediately
      })
      .catch((err) => {
        console.error('Error:', err);
        setTimeout(longPollServer, 5000); // Retry after 5 seconds on error
      });
  }
  longPollServer();
  ```

- **Server Implementation (Node.js)**:
  ```javascript
  app.get('/long-poll', (req, res) => {
    // Simulate a delay until new data is available
    setTimeout(() => {
      res.json({ message: 'New data available!' });
    }, Math.random() * 10000); // Random delay up to 10 seconds
  });
  ```

---

### **Key Differences Between Polling and Long Polling**
| Aspect            | Polling                              | Long Polling                       |
|--------------------|---------------------------------------|-------------------------------------|
| **Request Timing** | Fixed intervals                      | Sent only after receiving a response |
| **Efficiency**     | Less efficient due to constant checks | More efficient for infrequent updates |
| **Server Load**    | High due to frequent requests         | Lower because requests are spaced out |
| **Use Case**       | Real-time applications with frequent updates | Rarely changing data (e.g., notifications) |

---

### **When to Use Polling or Long Polling**
1. **Polling**:
   - For simple applications with frequent updates.
   - When modern protocols like WebSockets aren’t supported.
   - When you don’t need high efficiency.

2. **Long Polling**:
   - For applications where updates are infrequent but need to be received as soon as they occur (e.g., chat apps, notifications).
   - When WebSockets aren’t feasible due to infrastructure constraints.
------------

# **WebSockets: A Deep Dive**

WebSockets provide a **full-duplex communication protocol** that enables persistent, real-time interaction between a client and a server over a single TCP connection.

---

### **How WebSockets Work**
1. **Handshake**:
   - The WebSocket connection starts with an **HTTP handshake** initiated by the client.
   - The client sends a `GET` request to the server with an `Upgrade` header indicating that it wants to establish a WebSocket connection.
   - If the server supports WebSockets, it responds with a `101 Switching Protocols` status, and the connection is upgraded.

   **Example Handshake:**
   - **Client Request:**
     ```http
     GET /socket HTTP/1.1
     Host: example.com
     Upgrade: websocket
     Connection: Upgrade
     Sec-WebSocket-Key: random-base64-key
     Sec-WebSocket-Version: 13
     ```
   - **Server Response:**
     ```http
     HTTP/1.1 101 Switching Protocols
     Upgrade: websocket
     Connection: Upgrade
     Sec-WebSocket-Accept: hashed-base64-key
     ```

2. **Persistent Connection**:
   - After the handshake, the connection remains open and allows **low-latency, bidirectional communication** without the need to repeatedly establish new connections.

3. **Data Transmission**:
   - Both client and server can send messages at any time.
   - Messages are transmitted in a lightweight binary or text format, reducing overhead compared to traditional HTTP.

---

### **Key Features of WebSockets**
1. **Bidirectional Communication**:
   - Both client and server can send and receive messages without explicitly polling or waiting.
2. **Low Latency**:
   - WebSockets eliminate the overhead of HTTP headers in each message, resulting in faster communication.
3. **Persistent Connection**:
   - Once established, the connection remains open, avoiding repeated connection setups.
4. **Lightweight**:
   - Uses frames instead of full HTTP requests, minimizing data transfer.
5. **Event-Based**:
   - WebSocket APIs use events like `onopen`, `onmessage`, and `onclose` for handling communication.

---

### **WebSockets in Practice**

#### **Client-Side Example (JavaScript)**:
```javascript
// Establish WebSocket connection
const socket = new WebSocket('ws://example.com/socket');

// Handle connection open
socket.onopen = () => {
  console.log('WebSocket connection established');
  socket.send('Hello, Server!');
};

// Handle incoming messages
socket.onmessage = (event) => {
  console.log('Message from server:', event.data);
};

// Handle connection close
socket.onclose = () => {
  console.log('WebSocket connection closed');
};

// Handle errors
socket.onerror = (error) => {
  console.error('WebSocket error:', error);
};
```

#### **Server-Side Example (Node.js using WebSocket Library)**:
```javascript
const WebSocket = require('ws');

// Create WebSocket server
const wss = new WebSocket.Server({ port: 8080 });

wss.on('connection', (ws) => {
  console.log('Client connected');

  // Handle messages from the client
  ws.on('message', (message) => {
    console.log('Received:', message);
    ws.send(`Echo: ${message}`); // Send response back
  });

  // Handle client disconnection
  ws.on('close', () => {
    console.log('Client disconnected');
  });
});
```

---

### **When to Use WebSockets**
1. **Real-Time Chat Applications**:
   - Instant messaging platforms like WhatsApp or Slack.
2. **Live Data Feeds**:
   - Financial market updates, sports scores, or news alerts.
3. **Multiplayer Online Games**:
   - Fast-paced games requiring low-latency updates.
4. **Collaborative Tools**:
   - Applications like Google Docs for real-time document editing.
5. **IoT Applications**:
   - Controlling and monitoring IoT devices in real time.

---

### **Challenges with WebSockets**
1. **Scalability**:
   - Persistent connections can strain server resources, especially with many concurrent users.
   - Solutions like load balancing and WebSocket-compatible proxies (e.g., NGINX) are needed.
2. **Fallbacks**:
   - Older browsers or firewalls might not support WebSockets.
   - Libraries like Socket.IO handle graceful degradation to HTTP-based alternatives (e.g., long polling).
3. **Security**:
   - Always use **WSS (WebSocket Secure)** over HTTPS to encrypt communication.
   - Implement authentication and rate-limiting to prevent abuse.

---

# **Socket.IO**

Socket.IO is a popular library built on top of WebSockets, designed to enable **real-time, bidirectional, and event-driven communication** between clients (browsers) and servers. It abstracts away the complexities of WebSocket protocols and provides additional features like automatic reconnection, room-based communication, and fallback mechanisms for unsupported browsers.

---

### **How Socket.IO Works**
1. **WebSocket Protocol**:  
   Socket.IO primarily uses WebSockets for communication when supported by the client and server.  
   Example: A WebSocket connection is established with `ws://`.

2. **Fallbacks**:  
   For environments where WebSockets are not supported, Socket.IO gracefully falls back to HTTP long polling or other methods to maintain compatibility.

3. **Event-Driven Communication**:  
   Both the server and the client use a shared event-based model for communication. Custom events can be defined and emitted in either direction.

---

### **Features of Socket.IO**
1. **Automatic Reconnection**:  
   If a connection drops, Socket.IO attempts to reconnect automatically.
2. **Event-Based Model**:  
   Custom events can be defined, emitted, and handled using `.on()` and `.emit()` methods.
3. **Room Support**:  
   Clients can join rooms, enabling targeted broadcasting of messages.
4. **Binary Support**:  
   Transfer binary data like images, videos, or files.
5. **Acks (Acknowledgments)**:  
   Callbacks can be passed with `.emit()` to confirm message delivery.
6. **Cross-Browser Compatibility**:  
   Works seamlessly across different browsers and platforms.
7. **Middleware**:  
   Middleware functions can be used to handle custom authentication and validation.
8. **Debugging Tools**:  
   Built-in debugging with namespaces.

---

### **Socket.IO Architecture**

#### 1. **Server-Side**
The server handles multiple client connections, broadcasts messages, and manages rooms.  
It runs on Node.js and leverages the `socket.io` package.

#### 2. **Client-Side**
The client is typically a browser or a mobile app that communicates with the server using the `socket.io-client` library.

---

### **Socket.IO Installation**
1. **Server-Side** (Node.js):
   ```bash
   npm install socket.io
   ```

2. **Client-Side** (Browser):
   ```bash
   npm install socket.io-client
   ```

---

### **Socket.IO Server-Side Example**
```javascript
const { Server } = require("socket.io");
const io = new Server(3000);

io.on("connection", (socket) => {
  console.log("A user connected:", socket.id);

  // Listening for a custom event
  socket.on("message", (data) => {
    console.log("Message received:", data);

    // Broadcasting to all connected clients
    io.emit("broadcast", `User ${socket.id} says: ${data}`);
  });

  // Handling disconnection
  socket.on("disconnect", () => {
    console.log("A user disconnected:", socket.id);
  });
});
```

---

### **Socket.IO Client-Side Example**
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Socket.IO Client</title>
</head>
<body>
  <script src="https://cdn.socket.io/4.6.1/socket.io.min.js"></script>
  <script>
    const socket = io("http://localhost:3000");

    // Emit an event to the server
    socket.emit("message", "Hello Server!");

    // Listen for broadcast events
    socket.on("broadcast", (data) => {
      console.log("Broadcast received:", data);
    });
  </script>
</body>
</html>
```

---

### **Rooms in Socket.IO**
Rooms allow grouping clients to send targeted messages.

#### **Server-Side**
```javascript
io.on("connection", (socket) => {
  socket.on("joinRoom", (room) => {
    socket.join(room);
    console.log(`${socket.id} joined room: ${room}`);
  });

  socket.on("messageToRoom", ({ room, message }) => {
    io.to(room).emit("roomMessage", message);
  });
});
```

#### **Client-Side**
```javascript
socket.emit("joinRoom", "chatRoom1");
socket.emit("messageToRoom", { room: "chatRoom1", message: "Hello Room!" });

socket.on("roomMessage", (msg) => {
  console.log("Message for room:", msg);
});
```

---

### **Namespaces in Socket.IO**
Namespaces allow segregating connections by endpoints (like API endpoints for different services).

#### **Server-Side**
```javascript
const chatNamespace = io.of("/chat");
chatNamespace.on("connection", (socket) => {
  console.log("Connected to chat namespace:", socket.id);
});
```

#### **Client-Side**
```javascript
const chatSocket = io("http://localhost:3000/chat");
chatSocket.on("connect", () => {
  console.log("Connected to chat namespace!");
});
```

---

### **Socket.IO Acknowledgments**
Acknowledgments are callbacks to confirm message delivery.

#### **Server-Side**
```javascript
socket.on("sendMessage", (msg, callback) => {
  console.log("Message received:", msg);
  callback("Message delivered!");
});
```

#### **Client-Side**
```javascript
socket.emit("sendMessage", "Hello", (response) => {
  console.log(response); // Output: "Message delivered!"
});
```

---

### **Socket.IO Debugging**
Enable debugging by setting the environment variable `DEBUG`:
```bash
DEBUG=socket.io:* node server.js
```

---

### **Socket.IO Use Cases**
1. **Chat Applications**:
   - Real-time group chats or private messaging.
2. **Live Dashboards**:
   - Stock market updates, server monitoring.
3. **Collaborative Applications**:
   - Real-time document editing.
4. **Gaming**:
   - Multiplayer online games.
5. **IoT**:
   - Device monitoring and control in real-time.

---

### **Challenges and Limitations**
1. **Scalability**:
   - Persistent connections can strain resources.
   - Solutions: Clustering, horizontal scaling with Redis or adapters.
2. **Security**:
   - Use HTTPS and proper authentication to prevent unauthorized access.
3. **Browser Compatibility**:
   - Although Socket.IO handles fallbacks, older browsers might still face issues.

---

## **Differences Between WebSocket and Socket.IO**

1. **Purpose**:  
   - **WebSocket**: A communication protocol for full-duplex communication over a single TCP connection.  
   - **Socket.IO**: A library built on top of WebSocket with added features like fallbacks, event handling, and reconnection.

2. **Fallback Support**:  
   - **WebSocket**: No fallback; fails if WebSocket is not supported by the browser.  
   - **Socket.IO**: Provides fallbacks like HTTP long polling.

3. **Event Handling**:  
   - **WebSocket**: Minimal support with `onmessage`, `onopen`, and `onclose` events.  
   - **Socket.IO**: Fully event-driven with custom event support.

4. **Ease of Use**:  
   - **WebSocket**: Requires more effort to manage reconnection and custom logic.  
   - **Socket.IO**: Simplifies development with built-in reconnection, room management, and namespaces.

5. **Browser Compatibility**:  
   - **WebSocket**: Limited to browsers with WebSocket support.  
   - **Socket.IO**: Works on a broader range of browsers using fallbacks.

6. **Transport Layer**:  
   - **WebSocket**: Only WebSocket protocol.  
   - **Socket.IO**: Starts with long polling and upgrades to WebSocket when available.

7. **Authentication**:  
   - **WebSocket**: Requires custom implementation.  
   - **Socket.IO**: Supports middleware for authentication.

8. **Rooms and Namespaces**:  
   - **WebSocket**: Not supported natively; needs custom implementation.  
   - **Socket.IO**: Built-in support for rooms and namespaces.

9. **Protocol**:  
   - **WebSocket**: Standardized protocol (RFC 6455).  
   - **Socket.IO**: Custom protocol layered on WebSocket and other transports.

10. **Binary Data**:  
    - **WebSocket**: Supports binary data natively.  
    - **Socket.IO**: Supports binary data with additional abstractions.

11. **Debugging**:  
    - **WebSocket**: Limited debugging tools.  
    - **Socket.IO**: Built-in debugging with namespaces and logging.

12. **Server-Side Support**:  
    - **WebSocket**: Requires libraries like `ws` or `WebSocketServer`.  
    - **Socket.IO**: Provides its own server implementation.

-------

# **How Socket.IO Works Behind the Scenes**

Socket.IO is built on top of the WebSocket protocol but provides additional features and abstraction to handle real-time communication efficiently. Here's a step-by-step breakdown of how it works:

---

### **1. Connection Establishment**

- **Initial Handshake**:  
  When a client connects to the server, an HTTP request is made to perform the handshake. This step establishes the initial communication and negotiates the transport protocol (e.g., WebSocket, long polling).  
  - URL: `http://server_url/socket.io/`  
  - Handshake response contains connection details (session ID, supported protocols, etc.).

- **Upgrading Transport**:  
  After the handshake, Socket.IO attempts to upgrade the connection from HTTP (long polling) to WebSocket if supported by the client and server.  
  - If WebSocket is unavailable, it falls back to long polling or another supported transport.

---

### **2. Messaging and Event Handling**

- **Event-Based Communication**:  
  Communication happens through events. Both client and server can emit and listen to events.  
  Example:  
  - Client emits an event:
    ```javascript
    socket.emit('message', 'Hello from client!');
    ```
  - Server listens for the event:
    ```javascript
    io.on('connection', (socket) => {
      socket.on('message', (data) => {
        console.log(data);
      });
    });
    ```
---

### **3. Namespaces and Rooms**

- **Namespaces**:  
  Socket.IO allows creating custom namespaces (like endpoints) to separate logic.  
  - Mechanism: The client connects to a specific namespace (e.g., `/chat`), and the server handles events for that namespace.
  - Server: 
    ```javascript
    const chatNamespace = io.of('/chat');
    chatNamespace.on('connection', (socket) => {
      console.log('Connected to chat namespace');
    });
    ```
  - Client:
    ```javascript
    const socket = io('/chat');
    ```

- **Rooms**:  
  Rooms allow grouping sockets, enabling broadcasting to specific groups.  
  - Mechanism: A socket joins a room, and messages can be sent to all sockets in that room.
  - Server: 
    ```javascript
    socket.join('room1');
    io.to('room1').emit('message', 'Hello room1!');
    ```

---

### **4. Heartbeat and Reconnection**

- **Heartbeat Mechanism**:  
  Socket.IO uses a **ping-pong** mechanism to keep the connection alive.  
  - Server sends a `ping` message periodically.  
  - Client responds with `pong`.  
  - If no response is received within a timeout, the connection is considered lost.

- **Automatic Reconnection**:  
  If the connection drops, Socket.IO attempts to reconnect automatically.  
  - The client tries reconnecting with an exponential backoff strategy.  
  - Example:
    ```javascript
    const socket = io({
      reconnection: true,
      reconnectionAttempts: 5,
      reconnectionDelay: 1000,
    });
    ```

---

### **5. Fallback Mechanisms**

If WebSocket is not supported:
1. The server switches to HTTP long polling.
2. The client sends an HTTP request to the server at regular intervals to fetch new messages.

---

### **6. Binary Data Support**

- Socket.IO supports sending and receiving binary data (e.g., files, images).  
- Mechanism:
  - Binary data is chunked into smaller pieces if necessary.
  - Sent over WebSocket or fallback transport.

Example:
```javascript
socket.emit('binaryEvent', new Blob([arrayBuffer]));
```

---

### **7. Middleware and Authentication**

- **Middleware**:  
  Middleware functions process data before the event is handled.  
  Example: Authenticate a user before establishing a connection.  
  ```javascript
  io.use((socket, next) => {
    const token = socket.handshake.query.token;
    if (isValidToken(token)) {
      next();
    } else {
      next(new Error('Authentication error'));
    }
  });
  ```

---

### **8. Serialization and Protocol**
- Messages are serialized into a specific format before transmission:
  - Contains the **event name**, **data**, and **metadata** (e.g., acknowledgment, room info).
- Protocol is custom-built over WebSocket for enhanced functionality.

---

## How `socket.emit` works in background (What happened when we send a message)

---

### **1. Event Registration**
When you call `socket.emit(eventName, data)`, you specify:
- An **event name** (e.g., `'message'`).
- Data to send (e.g., a string, object, or binary data).

Example:
```javascript
socket.emit('message', { text: 'Hello, Server!' });
```

---

### **2. Data Serialization**
Before the message is sent, Socket.IO performs the following:
- **Event Packaging**:
  - Wraps the event name and data into a structured message format.
  - Adds metadata (e.g., event type, namespace, and room if applicable).
- **Serialization**:
  - Converts the structured message into a format suitable for transmission, typically JSON or binary for binary data.

Example of a serialized payload:
```json
{
  "type": "event",
  "name": "message",
  "data": { "text": "Hello, Server!" }
}
```

---

### **3. Transport Layer Selection**
Socket.IO determines the transport mechanism:
- **WebSocket** (if available): The serialized message is sent directly over the WebSocket connection.  
- **Fallback Transport** (if WebSocket is unavailable): Uses HTTP long polling or another supported transport to send the message.  

---

### **4. Message Transmission**
The serialized message is sent through the selected transport:
- For **WebSocket**:
  - The message is sent over the existing TCP connection in the form of WebSocket frames.
  - Frames are lightweight and designed for low latency.
- For **Long Polling**:
  - The message is sent as part of an HTTP request to the server.
  - The server processes the request and responds with a confirmation.

---

### **5. Server Reception**
The server (Socket.IO server library):
- **Parses the Incoming Data**:
  - Identifies the event name (`message`) and its associated data.
- **Processes the Event**:
  - Passes the data to the registered event listener for the given event name.

Example:
```javascript
io.on('connection', (socket) => {
  socket.on('message', (data) => {
    console.log('Message received:', data.text);
  });
});
```

---

### **6. Acknowledgment (Optional)**
If you specify an acknowledgment callback when emitting, the server can confirm message receipt:
- The client includes an acknowledgment callback in the serialized payload.
- The server executes the callback after processing the event.

Example:
```javascript
// Client
socket.emit('message', { text: 'Hello, Server!' }, (ack) => {
  console.log('Server acknowledged:', ack);
});

// Server
socket.on('message', (data, callback) => {
  console.log('Message received:', data.text);
  callback('Received your message!');
});
```

---

# **Server-Sent Events (SSE)**

**Server-Sent Events (SSE)** is a mechanism that allows a server to push real-time updates to the browser over a single, long-lived HTTP connection. Unlike WebSockets, where both client and server can send messages, SSE allows only the server to send updates to the client, which is ideal for scenarios like live notifications, news feeds, and real-time data updates.

---

### **How Does SSE Work?**

1. **Client Request**:
   - The client (browser) opens an HTTP connection to the server and requests the event stream using the `EventSource` API.
   
   Example:
   ```javascript
   const eventSource = new EventSource('https://example.com/events');
   ```

2. **Server Response**:
   - The server sends the initial response with the **Content-Type** header set to `text/event-stream`, indicating that the server will send a continuous stream of events.

   Example of server response:
   ```
   HTTP/1.1 200 OK
   Content-Type: text/event-stream
   Cache-Control: no-cache
   ```

3. **Sending Events**:
   - The server sends updates in a specific format that the client can interpret.
   - Each event is sent as a text block with the following format:
     ```
     event: eventName\n
     data: eventData\n
     id: eventId\n
     retry: reconnectDelay\n
     \n
     ```
   - **`event`**: The event name (optional).
   - **`data`**: The actual data being sent (mandatory).
   - **`id`**: A unique ID for the event, useful for reconnection (optional).
   - **`retry`**: Reconnection delay (optional).

   Example:
   ```
   event: message
   data: {"text": "New message received"}
   \n
   ```

4. **Receiving Events**:
   - The client listens for events using the `EventSource` API.
   - The client automatically receives new messages as the server sends them.
   
   Example of receiving the event:
   ```javascript
   eventSource.onmessage = function(event) {
     console.log('New message:', event.data);
   };
   ```

5. **Reconnection**:
   - If the connection between the client and server is lost, the client will automatically try to reconnect based on the `retry` value provided by the server. This is done without needing to refresh the page.

---

### **Key Features of SSE**

1. **Unidirectional Communication**:
   - Only the server can send messages to the client. The client cannot send messages back via the same connection.
   
2. **HTTP-Based**:
   - SSE works over standard HTTP, meaning it is supported by most web servers and proxies.

3. **Automatic Reconnection**:
   - If the connection is lost, the browser will automatically reconnect to the server with an optional delay.

4. **Text Format**:
   - Data sent from the server is text-based, typically formatted in JSON or plain text.

5. **Event Stream**:
   - SSEs are streamed over a single, long-lived HTTP connection, which allows for continuous updates without repeated HTTP requests.

---

### **Use Cases for SSE**

- **Live Notifications**: For sending real-time alerts or messages (e.g., email notifications, chat applications).
- **News Feeds**: Continuously pushing updates for news or blog posts.
- **Live Data Feeds**: Sending live updates for stock prices, sports scores, weather, etc.
- **Collaborative Applications**: Sending real-time changes in a collaborative app (e.g., collaborative document editing).
- **Monitoring Dashboards**: For pushing real-time metrics or system status updates.

---

### **SSE Example**

#### **Server (Node.js using Express)**:
```javascript
const express = require('express');
const app = express();

app.get('/events', (req, res) => {
  res.setHeader('Content-Type', 'text/event-stream');
  res.setHeader('Cache-Control', 'no-cache');
  res.setHeader('Connection', 'keep-alive');
  
  let count = 0;
  setInterval(() => {
    res.write(`event: message\n`);
    res.write(`data: {"text": "New message number ${count}"}\n\n`);
    count++;
  }, 2000); // Send an update every 2 seconds
});

app.listen(3000, () => {
  console.log('Server is listening on port 3000');
});
```

#### **Client (HTML + JavaScript)**:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>SSE Example</title>
</head>
<body>
  <h1>Server-Sent Events</h1>
  <div id="messages"></div>

  <script>
    const eventSource = new EventSource('http://localhost:3000/events');
    
    eventSource.onmessage = function(event) {
      const messageData = JSON.parse(event.data);
      const messageDiv = document.createElement('div');
      messageDiv.textContent = messageData.text;
      document.getElementById('messages').appendChild(messageDiv);
    };
    
    eventSource.onerror = function() {
      console.error('Error with the event source.');
    };
  </script>
</body>
</html>
```

---

## **What is EventSource?**

**EventSource** is a built-in JavaScript API that allows the browser to receive real-time updates from a server over a single HTTP connection using **Server-Sent Events (SSE)**. It is designed to make it easy to listen for and handle events sent by the server, enabling real-time, one-way communication from the server to the client.

---

### **How EventSource Works**

1. **Client-Side API**:
   - The client (usually a browser) creates an `EventSource` object and provides the URL of the server endpoint that will stream events.

2. **Server-Sent Events**:
   - The server responds to the request with a continuous stream of events, formatted in a specific way. These events are sent as text data (typically JSON) over the HTTP connection.

3. **Listening for Events**:
   - The client uses event listeners (e.g., `onmessage`, `onerror`) to handle incoming events, which are automatically processed by the browser.

4. **Automatic Reconnection**:
   - If the connection is lost (e.g., due to network issues), the browser automatically tries to reconnect to the server after a short delay, ensuring that data delivery is as continuous as possible.

---
