# Advanced MongoDB Use Cases

## Implementing Transactions
### 1. Using Mongoose Transactions

**Handle multiple operations atomically** with Mongoose transactions.

**Example: Using Mongoose Transactions**

1. **Define Transaction Operations**:

   ```javascript
   // routes/orderRoutes.js
   const express = require('express');
   const mongoose = require('mongoose');
   const Order = require('../models/Order');
   const Product = require('../models/Product');
   const router = express.Router();

   router.post('/create-order', async (req, res) => {
     const session = await mongoose.startSession();
     session.startTransaction();
     try {
       const { productId, quantity } = req.body;
       
       // Create an order
       const order = await Order.create([{ productId, quantity }], { session });
       
       // Update product stock
       await Product.updateOne({ _id: productId }, { $inc: { stock: -quantity } }, { session });

       await session.commitTransaction();
       session.endSession();
       res.status(201).send(order);
     } catch (error) {
       await session.abortTransaction();
       session.endSession();
       res.status(500).send('Error creating order');
     }
   });

   module.exports = router;
   ```

2. **Example Request**:
   - POST `/create-order` initiates a transaction, ensuring both order creation and stock update are atomic.

- **Transactions**: Use Mongoose transactions to ensure multiple operations succeed or fail together, maintaining data consistency.

### 2. Handling Rollbacks and Consistency

**Ensure atomic operations** by handling rollbacks and maintaining consistency with Mongoose transactions.

**Example: Handling Rollbacks and Consistency**

1. **Transaction with Rollback**:

   ```javascript
   // routes/orderRoutes.js
   const express = require('express');
   const mongoose = require('mongoose');
   const Order = require('../models/Order');
   const Product = require('../models/Product');
   const router = express.Router();

   router.post('/create-order', async (req, res) => {
     const session = await mongoose.startSession();
     session.startTransaction();
     try {
       const { productId, quantity } = req.body;
       
       // Create an order
       const order = await Order.create([{ productId, quantity }], { session });

       // Update product stock
       await Product.updateOne({ _id: productId }, { $inc: { stock: -quantity } }, { session });

       // Commit transaction
       await session.commitTransaction();
       session.endSession();
       res.status(201).send(order);
     } catch (error) {
       // Rollback transaction
       await session.abortTransaction();
       session.endSession();
       console.error('Transaction failed, rolled back:', error);
       res.status(500).send('Transaction failed');
     }
   });

   module.exports = router;
   ```

2. **Consistency**:
   - In case of failure, the transaction is rolled back to ensure the database remains in a consistent state.

- **Rollbacks and Consistency**: Use transactions to handle failures gracefully, ensuring data integrity by rolling back operations if any part of the transaction fails.



## Real-time Data Updates
### 1. Using Change Streams

**Track and respond to real-time changes** in MongoDB with Change Streams.

**Example: Using Change Streams**

1. **Setup Change Streams**:

   ```javascript
   // app.js
   const express = require('express');
   const mongoose = require('mongoose');
   const Order = require('./models/Order'); // Assume Order model is defined

   const app = express();

   // Connect to MongoDB
   mongoose.connect('mongodb://localhost:27017/mydb', { useNewUrlParser: true, useUnifiedTopology: true });

   // Start listening for changes
   const changeStream = Order.watch(); // Watch for changes in the Order collection

   changeStream.on('change', (change) => {
     console.log('Change detected:', change);
     // Handle change event
   });

   app.listen(3000, () => {
     console.log('Server running on port 3000');
   });
   ```

2. **Change Stream Output**:
   - Logs change events like insertions, updates, and deletions in real-time.

- **Change Streams**: Leverage MongoDB Change Streams to monitor and respond to real-time updates, enabling dynamic applications and live data features.

### 2. Integrating with WebSockets for Live Updates

**Enable live updates** by combining MongoDB Change Streams with WebSocket for real-time data communication.

**Example: WebSocket Integration**

1. **Setup WebSocket Server**:

   ```javascript
   // app.js
   const express = require('express');
   const http = require('http');
   const mongoose = require('mongoose');
   const WebSocket = require('ws');
   const Order = require('./models/Order'); // Assume Order model is defined

   const app = express();
   const server = http.createServer(app);
   const wss = new WebSocket.Server({ server });

   // Connect to MongoDB
   mongoose.connect('mongodb://localhost:27017/mydb', { useNewUrlParser: true, useUnifiedTopology: true });

   // WebSocket connection handler
   wss.on('connection', (ws) => {
     console.log('Client connected');
     
     // Watch for MongoDB changes
     const changeStream = Order.watch();

     changeStream.on('change', (change) => {
       console.log('Change detected:', change);
       ws.send(JSON.stringify(change)); // Send change to WebSocket client
     });

     ws.on('close', () => {
       console.log('Client disconnected');
       changeStream.close(); // Close Change Stream on client disconnect
     });
   });

   server.listen(3000, () => {
     console.log('Server running on port 3000');
   });
   ```

2. **Client-Side WebSocket**:
   - Connect to WebSocket server to receive live updates.

- **WebSocket Integration**: Combine MongoDB Change Streams with WebSockets to push real-time updates to clients, enhancing live data features and user experience.

