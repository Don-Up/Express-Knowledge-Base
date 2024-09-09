# WebSocket Integration

## Setting Up WebSocket with Socket.IO
### 1. Real-time Communication in Express

**Integrate WebSocket** using Socket.IO for real-time communication in Express.

**Example: Setting Up WebSocket**

1. **Setup Express App with Socket.IO**:

   ```javascript
   // server.js
   const express = require('express');
   const http = require('http');
   const socketIo = require('socket.io');
   const app = express();
   const server = http.createServer(app);
   const io = socketIo(server);

   // Serve static files
   app.use(express.static('public'));

   // Handle WebSocket connections
   io.on('connection', (socket) => {
     console.log('A user connected');

     // Handle messages from clients
     socket.on('message', (data) => {
       console.log('Message received:', data);
       // Broadcast message to all connected clients
       io.emit('message', data);
     });

     // Handle disconnection
     socket.on('disconnect', () => {
       console.log('User disconnected');
     });
   });

   const PORT = process.env.PORT || 3000;
   server.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **Client-side JavaScript** (e.g., in `public/index.html`):

   ```html
   <!DOCTYPE html>
   <html>
   <head>
     <title>Socket.IO Example</title>
   </head>
   <body>
     <script src="/socket.io/socket.io.js"></script>
     <script>
       const socket = io();
   
       // Send a message to the server
       socket.emit('message', 'Hello, server!');
   
       // Listen for messages from the server
       socket.on('message', (data) => {
         console.log('Message from server:', data);
       });
     </script>
   </body>
   </html>
   ```

- **Use `socket.io`** for WebSocket integration.
- **Set up a WebSocket server** and handle connections.
- **Broadcast messages** and manage real-time communication.

### 2. Handling Socket Connections and Events

**Handle WebSocket connections** and events using Socket.IO in Express.

**Example: Managing Socket Connections and Events**

1. **Setup Express App with Socket.IO**:

   ```javascript
   // server.js
   const express = require('express');
   const http = require('http');
   const socketIo = require('socket.io');
   const app = express();
   const server = http.createServer(app);
   const io = socketIo(server);

   // Handle WebSocket connections
   io.on('connection', (socket) => {
     console.log('A user connected:', socket.id);

     // Event: User sends a message
     socket.on('sendMessage', (message) => {
       console.log('Message received:', message);
       io.emit('broadcastMessage', message); // Broadcast message to all clients
     });

     // Event: User disconnects
     socket.on('disconnect', () => {
       console.log('User disconnected:', socket.id);
     });
   });

   const PORT = process.env.PORT || 3000;
   server.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **Client-side JavaScript** (e.g., in `public/index.html`):

   ```html
   <!DOCTYPE html>
   <html>
   <head>
     <title>Socket.IO Example</title>
   </head>
   <body>
     <script src="/socket.io/socket.io.js"></script>
     <script>
       const socket = io();
   
       // Send a message to the server
       socket.emit('sendMessage', 'Hello, server!');
   
       // Listen for broadcasted messages from the server
       socket.on('broadcastMessage', (message) => {
         console.log('Message from server:', message);
       });
     </script>
   </body>
   </html>
   ```

- **Handle socket events** like `connection`, `disconnect`, and custom events.
- **Emit and listen for messages** between server and clients.
- **Broadcast messages** to all connected clients.