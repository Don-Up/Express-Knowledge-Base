# CRUD Operations

## Implementing Create, Read, Update, Delete
#### 1. Handling Data with Mongoose Methods

**Implement** Create, Read, Update, and Delete operations using Mongoose methods.

**Example: CRUD Operations**

1. **Define Schema and Model**:

   ```javascript
   // models/User.js
   const mongoose = require('mongoose');

   const userSchema = new mongoose.Schema({
     name: { type: String, required: true },
     email: { type: String, required: true, unique: true },
     age: Number,
   });

   const User = mongoose.model('User', userSchema);

   module.exports = User;
   ```

2. **CRUD Operations in Express**:

   ```javascript
   // server.js
   const express = require('express');
   const mongoose = require('mongoose');
   const User = require('./models/User');
   
   const app = express();
   app.use(express.json());
   
   const mongoURI = 'mongodb://localhost:27017/mydatabase';
   
   mongoose.connect(mongoURI, {
     useNewUrlParser: true,
     useUnifiedTopology: true,
   });
   
   // Create
   app.post('/users', async (req, res) => {
     try {
       const user = new User(req.body);
       await user.save();
       res.status(201).send(user);
     } catch (error) {
       res.status(400).send(error);
     }
   });
   
   // Read
   app.get('/users/:id', async (req, res) => {
     try {
       const user = await User.findById(req.params.id);
       if (!user) return res.status(404).send('User not found');
       res.send(user);
     } catch (error) {
       res.status(400).send(error);
     }
   });
   
   // Update
   app.put('/users/:id', async (req, res) => {
     try {
       const user = await User.findByIdAndUpdate(req.params.id, req.body, { new: true });
       if (!user) return res.status(404).send('User not found');
       res.send(user);
     } catch (error) {
       res.status(400).send(error);
     }
   });
   
   // Delete
   app.delete('/users/:id', async (req, res) => {
     try {
       const user = await User.findByIdAndDelete(req.params.id);
       if (!user) return res.status(404).send('User not found');
       res.send('User deleted');
     } catch (error) {
       res.status(400).send(error);
     }
   });
   
   app.listen(3000, () => {
     console.log('Server running on port 3000');
   });
   ```

- **Use** Mongoose methods like `save()`, `findById()`, `findByIdAndUpdate()`, and `findByIdAndDelete()` for CRUD operations.

#### 2. Querying and Aggregation

**Implement** Create, Read, Update, and Delete operations with querying and aggregation.

**Example: CRUD with Querying and Aggregation**

1. **Define Schema and Model**:

   ```javascript
   // models/User.js
   const mongoose = require('mongoose');

   const userSchema = new mongoose.Schema({
     name: { type: String, required: true },
     email: { type: String, required: true, unique: true },
     age: Number,
   });

   const User = mongoose.model('User', userSchema);

   module.exports = User;
   ```

2. **CRUD Operations with Querying and Aggregation**:

   ```javascript
   // server.js
   const express = require('express');
   const mongoose = require('mongoose');
   const User = require('./models/User');
   
   const app = express();
   app.use(express.json());
   
   const mongoURI = 'mongodb://localhost:27017/mydatabase';
   
   mongoose.connect(mongoURI, {
     useNewUrlParser: true,
     useUnifiedTopology: true,
   });
   
   // Create
   app.post('/users', async (req, res) => {
     try {
       const user = new User(req.body);
       await user.save();
       res.status(201).send(user);
     } catch (error) {
       res.status(400).send(error);
     }
   });
   
   // Read with Querying
   app.get('/users', async (req, res) => {
     try {
       const users = await User.find(req.query); // Querying with URL parameters
       res.send(users);
     } catch (error) {
       res.status(400).send(error);
     }
   });
   
   // Update
   app.put('/users/:id', async (req, res) => {
     try {
       const user = await User.findByIdAndUpdate(req.params.id, req.body, { new: true });
       if (!user) return res.status(404).send('User not found');
       res.send(user);
     } catch (error) {
       res.status(400).send(error);
     }
   });
   
   // Delete
   app.delete('/users/:id', async (req, res) => {
     try {
       const user = await User.findByIdAndDelete(req.params.id);
       if (!user) return res.status(404).send('User not found');
       res.send('User deleted');
     } catch (error) {
       res.status(400).send(error);
     }
   });
   
   // Aggregation Example
   app.get('/users/age-stats', async (req, res) => {
     try {
       const stats = await User.aggregate([
         { $group: { _id: null, avgAge: { $avg: "$age" }, maxAge: { $max: "$age" }, minAge: { $min: "$age" } } }
       ]);
       res.send(stats);
     } catch (error) {
       res.status(400).send(error);
     }
   });
   
   app.listen(3000, () => {
     console.log('Server running on port 3000');
   });
   ```

- **Use** Mongoose's `find()`, `aggregate()`, and other methods for querying and data aggregation.



## Error Handling with MongoDB
#### 1. Managing Mongoose Errors

**Handle** errors in CRUD operations, managing Mongoose errors for robust applications.

**Example: Error Handling**

1. **Define Schema and Model**:

   ```javascript
   // models/User.js
   const mongoose = require('mongoose');

   const userSchema = new mongoose.Schema({
     name: { type: String, required: true },
     email: { type: String, required: true, unique: true },
     age: Number,
   });

   const User = mongoose.model('User', userSchema);

   module.exports = User;
   ```

2. **CRUD Operations with Error Handling**:

   ```javascript
   // server.js
   const express = require('express');
   const mongoose = require('mongoose');
   const User = require('./models/User');
   
   const app = express();
   app.use(express.json());
   
   const mongoURI = 'mongodb://localhost:27017/mydatabase';
   
   mongoose.connect(mongoURI, {
     useNewUrlParser: true,
     useUnifiedTopology: true,
   });
   
   // Create
   app.post('/users', async (req, res) => {
     try {
       const user = new User(req.body);
       await user.save();
       res.status(201).send(user);
     } catch (error) {
       if (error.name === 'ValidationError') {
         res.status(400).send({ error: error.message });
       } else if (error.code === 11000) {
         res.status(409).send({ error: 'Duplicate key error' });
       } else {
         res.status(500).send({ error: 'Internal server error' });
       }
     }
   });
   
   // Read
   app.get('/users/:id', async (req, res) => {
     try {
       const user = await User.findById(req.params.id);
       if (!user) return res.status(404).send('User not found');
       res.send(user);
     } catch (error) {
       res.status(400).send({ error: 'Invalid ID format' });
     }
   });
   
   // Update
   app.put('/users/:id', async (req, res) => {
     try {
       const user = await User.findByIdAndUpdate(req.params.id, req.body, { new: true });
       if (!user) return res.status(404).send('User not found');
       res.send(user);
     } catch (error) {
       res.status(400).send({ error: 'Invalid ID format or update failed' });
     }
   });
   
   // Delete
   app.delete('/users/:id', async (req, res) => {
     try {
       const user = await User.findByIdAndDelete(req.params.id);
       if (!user) return res.status(404).send('User not found');
       res.send('User deleted');
     } catch (error) {
       res.status(400).send({ error: 'Invalid ID format' });
     }
   });
   
   app.listen(3000, () => {
     console.log('Server running on port 3000');
   });
   ```

- **Handle** Mongoose errors with specific checks for validation errors, duplicate keys, and invalid IDs.

#### 2. Graceful Handling of Database Failures

**Gracefully handle** database failures in CRUD operations using proper error handling.

**Example: Graceful Error Handling**

1. **Define Schema and Model**:

   ```javascript
   // models/User.js
   const mongoose = require('mongoose');

   const userSchema = new mongoose.Schema({
     name: { type: String, required: true },
     email: { type: String, required: true, unique: true },
     age: Number,
   });

   const User = mongoose.model('User', userSchema);

   module.exports = User;
   ```

2. **CRUD Operations with Graceful Error Handling**:

   ```javascript
   // server.js
   const express = require('express');
   const mongoose = require('mongoose');
   const User = require('./models/User');
   
   const app = express();
   app.use(express.json());
   
   const mongoURI = 'mongodb://localhost:27017/mydatabase';
   
   mongoose.connect(mongoURI, {
     useNewUrlParser: true,
     useUnifiedTopology: true,
   }).catch(err => {
     console.error('Database connection error:', err);
   });
   
   // Middleware for error handling
   app.use((err, req, res, next) => {
     console.error('Unhandled error:', err.message);
     res.status(500).send('Internal Server Error');
   });
   
   // Create
   app.post('/users', async (req, res, next) => {
     try {
       const user = new User(req.body);
       await user.save();
       res.status(201).send(user);
     } catch (error) {
       next(error); // Pass error to middleware
     }
   });
   
   // Read
   app.get('/users/:id', async (req, res, next) => {
     try {
       const user = await User.findById(req.params.id);
       if (!user) return res.status(404).send('User not found');
       res.send(user);
     } catch (error) {
       next(error); // Pass error to middleware
     }
   });
   
   // Update
   app.put('/users/:id', async (req, res, next) => {
     try {
       const user = await User.findByIdAndUpdate(req.params.id, req.body, { new: true });
       if (!user) return res.status(404).send('User not found');
       res.send(user);
     } catch (error) {
       next(error); // Pass error to middleware
     }
   });
   
   // Delete
   app.delete('/users/:id', async (req, res, next) => {
     try {
       const user = await User.findByIdAndDelete(req.params.id);
       if (!user) return res.status(404).send('User not found');
       res.send('User deleted');
     } catch (error) {
       next(error); // Pass error to middleware
     }
   });
   
   app.listen(3000, () => {
     console.log('Server running on port 3000');
   });
   ```

- **Graceful Handling**: Use `try-catch` blocks in routes and a global error handler middleware to capture and handle errors gracefully.

