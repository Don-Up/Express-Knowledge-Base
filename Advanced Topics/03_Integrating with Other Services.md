# Integrating with Other Services

## Calling External APIs
### Making HTTP Requests with Axios, Fetch

### Express.js: Calling External APIs with Axios and Fetch

**Integrate** with external APIs in Express by making HTTP requests using Axios or Fetch.

**Example: Using Axios**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const axios = require('axios'); // npm install axios
   const app = express();
   const PORT = process.env.PORT || 3000;
   
   // Route to call an external API
   app.get('/external-data', async (req, res) => {
     try {
       const response = await axios.get('https://api.example.com/data');
       res.json(response.data); // Send API response as JSON
     } catch (error) {
       res.status(500).send('Error fetching external data');
     }
   });
   
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

**Example: Using Fetch (Node.js v18+)**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const { fetch } = require('node-fetch'); // npm install node-fetch@2
   const app = express();
   const PORT = process.env.PORT || 3000;
   
   // Route to call an external API
   app.get('/external-data', async (req, res) => {
     try {
       const response = await fetch('https://api.example.com/data');
       const data = await response.json();
       res.json(data); // Send API response as JSON
     } catch (error) {
       res.status(500).send('Error fetching external data');
     }
   });
   
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

- **Use Axios** or **Fetch** for HTTP requests.
- **Handle errors** to ensure reliability.
- **Send external API responses** directly to clients.



## Using Message Queues
### Integrating with RabbitMQ, Kafka

**Integrate** with RabbitMQ or Kafka for asynchronous processing in Express.

**Example: RabbitMQ with amqplib**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const amqp = require('amqplib'); // npm install amqplib
   const app = express();
   const PORT = process.env.PORT || 3000;
   
   // Connect to RabbitMQ
   const connectRabbitMQ = async () => {
     const conn = await amqp.connect('amqp://localhost');
     return conn.createChannel();
   };
   
   app.post('/send-message', async (req, res) => {
     try {
       const channel = await connectRabbitMQ();
       const queue = 'task_queue';
       const msg = 'Hello RabbitMQ!';
       await channel.assertQueue(queue, { durable: true });
       channel.sendToQueue(queue, Buffer.from(msg), { persistent: true });
       res.send('Message sent');
     } catch (error) {
       res.status(500).send('Error sending message');
     }
   });
   
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

**Example: Kafka with kafka-node**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const { KafkaClient, Producer } = require('kafka-node'); // npm install kafka-node
   const app = express();
   const PORT = process.env.PORT || 3000;
   
   // Connect to Kafka
   const client = new KafkaClient({ kafkaHost: 'localhost:9092' });
   const producer = new Producer(client);
   
   app.post('/send-message', (req, res) => {
     producer.send([{ topic: 'test-topic', messages: 'Hello Kafka!' }], (err, data) => {
       if (err) {
         res.status(500).send('Error sending message');
       } else {
         res.send('Message sent');
       }
     });
   });
   
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

- **Use RabbitMQ** or **Kafka** for message queuing.
- **Ensure proper connection handling** and **error management**.
- **Queue messages** for asynchronous processing or event handling.