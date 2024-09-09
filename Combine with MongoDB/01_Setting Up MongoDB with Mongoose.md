# Setting Up MongoDB with Mongoose

## Connecting Express to MongoDB
### 1. Setting Up Mongoose Connection

**Connect** Express to MongoDB using Mongoose.

**Example: Mongoose Connection Setup**

1. **Install Dependencies**:

   ```bash
   npm install mongoose
   ```

2. **Setup Mongoose Connection**:

   ```js
   // server.js
   const express = require('express');
   const mongoose = require('mongoose');
   
   const app = express();
   
   // MongoDB URI
   const mongoURI = 'mongodb://localhost:27017/mydatabase';
   
   // Connect to MongoDB
   mongoose.connect(mongoURI, {
     useNewUrlParser: true,
     useUnifiedTopology: true,
   }).then(() => {
     console.log('MongoDB connected');
   }).catch(err => {
     console.error('MongoDB connection error:', err);
   });
   
   app.listen(3000, () => {
     console.log('Server running on port 3000');
   });
   ```

- **Install** Mongoose and set up a MongoDB connection in `server.js`.
- **Handle** connection success and errors for robust integration.

### 2. Handling Connection Events

**Manage** MongoDB connection events using Mongoose.

**Example: Mongoose Connection with Event Handlers**

1. **Install Dependencies**:

   ```bash
   npm install mongoose
   ```

2. **Setup Mongoose Connection**:

   ```js
   // server.js
   const express = require('express');
   const mongoose = require('mongoose');
   
   const app = express();
   
   // MongoDB URI
   const mongoURI = 'mongodb://localhost:27017/mydatabase';
   
   // Connect to MongoDB
   mongoose.connect(mongoURI, {
     useNewUrlParser: true,
     useUnifiedTopology: true,
   });
   
   // Connection events
   mongoose.connection.on('connected', () => {
     console.log('MongoDB connected');
   });
   
   mongoose.connection.on('error', (err) => {
     console.error('MongoDB connection error:', err);
   });
   
   mongoose.connection.on('disconnected', () => {
     console.log('MongoDB disconnected');
   });
   
   app.listen(3000, () => {
     console.log('Server running on port 3000');
   });
   ```

- **Handle** `connected`, `error`, and `disconnected` events for better monitoring and troubleshooting.
- **Use** `mongoose.connection.on()` to listen for connection status changes.



## Defining Mongoose Models

### 1. Creating Schemas and Models

**Create** Mongoose schemas and models to interact with MongoDB.

**Example: Defining Mongoose Schemas and Models**

1. **Install Dependencies**:

   ```bash
   npm install mongoose
   ```

2. **Define Schema and Model**:

   ```javascript
   // models/User.js
   const mongoose = require('mongoose');

   // Define schema
   const userSchema = new mongoose.Schema({
     name: { type: String, required: true },
     email: { type: String, required: true, unique: true },
     age: Number,
   });

   // Create model
   const User = mongoose.model('User', userSchema);

   module.exports = User;
   ```

3. **Use Model in Express**:

   ```javascript
   // server.js
   const express = require('express');
   const mongoose = require('mongoose');
   const User = require('./models/User');
   
   const app = express();
   app.use(express.json());
   
   // MongoDB URI
   const mongoURI = 'mongodb://localhost:27017/mydatabase';
   
   mongoose.connect(mongoURI, {
     useNewUrlParser: true,
     useUnifiedTopology: true,
   });
   
   // Create a new user
   app.post('/users', async (req, res) => {
     try {
       const user = new User(req.body);
       await user.save();
       res.status(201).send(user);
     } catch (error) {
       res.status(400).send(error);
     }
   });
   
   app.listen(3000, () => {
     console.log('Server running on port 3000');
   });
   ```

- **Define** schemas and models to structure and interact with MongoDB documents.
- **Use** models to perform CRUD operations within your Express app.

### 1. Schema Valida tion and Hooks

**Implement** schema validation and hooks with Mongoose.

**Example: Schema Validation and Pre-save Hook**

1. **Define Schema with Validation and Hooks**:

   ```javascript
   // models/User.js
   const mongoose = require('mongoose');
   const validator = require('validator');

   // Define schema with validation
   const userSchema = new mongoose.Schema({
     name: { type: String, required: true },
     email: { 
       type: String, 
       required: true, 
       unique: true, 
       validate: [validator.isEmail, 'Invalid email']
     },
     age: { type: Number, min: 0 }
   });

   // Pre-save hook
   userSchema.pre('save', function(next) {
     if (this.age < 18) {
       this.age = 18; // Default to 18 if age is less
     }
     next();
   });

   // Create model
   const User = mongoose.model('User', userSchema);

   module.exports = User;
   ```

2. **Use Model in Express**:

   ```javascript
   // server.js
   const express = require('express');
   const mongoose = require('mongoose');
   const User = require('./models/User');
   
   const app = express();
   app.use(express.json());
   
   // MongoDB URI
   const mongoURI = 'mongodb://localhost:27017/mydatabase';
   
   mongoose.connect(mongoURI, {
     useNewUrlParser: true,
     useUnifiedTopology: true,
   });
   
   // Create a new user
   app.post('/users', async (req, res) => {
     try {
       const user = new User(req.body);
       await user.save();
       res.status(201).send(user);
     } catch (error) {
       res.status(400).send(error);
     }
   });
   
   app.listen(3000, () => {
     console.log('Server running on port 3000');
   });
   ```

- **Add** validation to schema fields and use hooks like `pre('save')` for additional logic before saving.