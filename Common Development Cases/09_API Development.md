# API Development

## RESTful API Design
### 1. Structuring Endpoints and Resources

### Express.js API Development: RESTful API Design

**Structuring endpoints and resources** follows REST principles, organizing URLs and HTTP methods to represent resources.

**Example: Structuring RESTful Endpoints**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();
   app.use(express.json());
   
   // Resource: Users
   app.get('/users', (req, res) => {
     // List all users
     res.json([{ id: 1, name: 'Alice' }]);
   });
   
   app.post('/users', (req, res) => {
     // Create a new user
     const user = req.body;
     res.status(201).json(user);
   });
   
   app.get('/users/:id', (req, res) => {
     // Get a specific user
     const userId = req.params.id;
     res.json({ id: userId, name: 'Alice' });
   });
   
   app.put('/users/:id', (req, res) => {
     // Update a specific user
     const userId = req.params.id;
     const updatedUser = req.body;
     res.json({ id: userId, ...updatedUser });
   });
   
   app.delete('/users/:id', (req, res) => {
     // Delete a specific user
     const userId = req.params.id;
     res.status(204).end(); // No content
   });
   
   const PORT = process.env.PORT || 3000;
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

- **GET /users**: List users.
- **POST /users**: Create user.
- **GET /users/:id**: Retrieve user by ID.
- **PUT /users/:id**: Update user by ID.
- **DELETE /users/:id**: Delete user by ID.

### 2. Best Practices for REST APIs

### Express.js API Development: Best Practices for REST APIs

**Best practices** ensure clear, maintainable, and scalable REST APIs.

**Example: Best Practices Implementation**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();
   app.use(express.json());
   
   // Use plural nouns for resources
   app.get('/api/users', (req, res) => {
     res.json([{ id: 1, name: 'Alice' }]);
   });
   
   app.post('/api/users', (req, res) => {
     const user = req.body;
     res.status(201).json(user);
   });
   
   app.get('/api/users/:id', (req, res) => {
     const userId = req.params.id;
     res.json({ id: userId, name: 'Alice' });
   });
   
   app.put('/api/users/:id', (req, res) => {
     const userId = req.params.id;
     const updatedUser = req.body;
     res.json({ id: userId, ...updatedUser });
   });
   
   app.delete('/api/users/:id', (req, res) => {
     const userId = req.params.id;
     res.status(204).end(); // No content
   });
   
   // Use meaningful status codes and error handling
   app.use((err, req, res, next) => {
     console.error(err);
     res.status(500).json({ error: 'Internal Server Error' });
   });
   
   const PORT = process.env.PORT || 3000;
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

- **Use plural nouns** for resources (e.g., `/users`).
- **Return meaningful status codes**.
- **Implement error handling** middleware.



## Error Handling in APIs
### 1. Sending Consistent Error Responses

**Consistent error responses** improve API usability by providing clear and uniform error information.

**Example: Consistent Error Responses**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();
   app.use(express.json());
   
   // Example route
   app.get('/api/data', (req, res, next) => {
     try {
       // Simulate error
       throw new Error('Something went wrong');
     } catch (err) {
       next(err);
     }
   });
   
   // Consistent error handling middleware
   app.use((err, req, res, next) => {
     console.error(err);
     res.status(err.statusCode || 500).json({
       error: {
         message: err.message || 'Internal Server Error',
         code: err.statusCode || 500,
       },
     });
   });
   
   const PORT = process.env.PORT || 3000;
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

- **Standardize error responses** with `error` object containing `message` and `code`.
- **Catch errors** in routes and pass to error handling middleware.
- **Use appropriate HTTP status codes**.

### 2. Managing HTTP Status Codes

**Manage HTTP status codes** to accurately reflect the outcome of API requests.

**Example: Managing HTTP Status Codes**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();
   app.use(express.json());
   
   // Example routes with status codes
   app.get('/api/data', (req, res) => {
     // Simulate successful data retrieval
     res.status(200).json({ data: 'Some data' });
   });
   
   app.post('/api/data', (req, res) => {
     if (!req.body.name) {
       // Bad request
       return res.status(400).json({ error: 'Name is required' });
     }
     // Simulate creation of resource
     res.status(201).json({ message: 'Resource created' });
   });
   
   app.put('/api/data/:id', (req, res) => {
     const id = req.params.id;
     if (id !== '1') {
       // Not found
       return res.status(404).json({ error: 'Resource not found' });
     }
     // Simulate successful update
     res.status(200).json({ message: 'Resource updated' });
   });
   
   app.delete('/api/data/:id', (req, res) => {
     const id = req.params.id;
     if (id !== '1') {
       // Not found
       return res.status(404).json({ error: 'Resource not found' });
     }
     // Simulate successful deletion
     res.status(204).end(); // No content
   });
   
   // Default error handling
   app.use((err, req, res, next) => {
     console.error(err);
     res.status(err.statusCode || 500).json({
       error: {
         message: err.message || 'Internal Server Error',
         code: err.statusCode || 500,
       },
     });
   });
   
   const PORT = process.env.PORT || 3000;
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

- **200 OK**: Successful GET requests.
- **201 Created**: Successful POST requests.
- **204 No Content**: Successful DELETE requests.
- **400 Bad Request**: Client-side input errors.
- **404 Not Found**: Resource not found.
- **500 Internal Server Error**: Unexpected server errors.



## Versioning APIs
### 1. Implementing API Versioning Strategies

**API versioning** manages changes by separating versions, ensuring backward compatibility.

**Example: Implementing API Versioning**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();
   app.use(express.json());
   
   // Version 1 Routes
   app.get('/api/v1/users', (req, res) => {
     res.json([{ id: 1, name: 'Alice' }]);
   });
   
   // Version 2 Routes
   app.get('/api/v2/users', (req, res) => {
     res.json([{ id: 1, name: 'Alice', email: 'alice@example.com' }]);
   });
   
   // Middleware for API versioning (optional)
   app.use((req, res, next) => {
     const version = req.headers['api-version'] || 'v1';
     req.apiVersion = version;
     next();
   });
   
   app.get('/api/users', (req, res) => {
     if (req.apiVersion === 'v2') {
       res.json([{ id: 1, name: 'Alice', email: 'alice@example.com' }]);
     } else {
       res.json([{ id: 1, name: 'Alice' }]);
     }
   });
   
   const PORT = process.env.PORT || 3000;
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

- **URL Versioning**: Use URL path segments (e.g., `/api/v1/`).
- **Header Versioning**: Specify version in request headers (`api-version`).
- **Middleware**: Optionally handle versioning dynamically.

### 2. Supporting Legacy API Versions

**Supporting legacy API versions** ensures backward compatibility while maintaining new versions.

**Example: Supporting Legacy API Versions**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();
   app.use(express.json());
   
   // Legacy Version 1 Routes
   app.get('/api/v1/users', (req, res) => {
     res.json([{ id: 1, name: 'Alice' }]);
   });
   
   // New Version 2 Routes
   app.get('/api/v2/users', (req, res) => {
     res.json([{ id: 1, name: 'Alice', email: 'alice@example.com' }]);
   });
   
   // Route for redirecting legacy versions (optional)
   app.use('/api/users', (req, res, next) => {
     if (req.headers['api-version'] === 'v1') {
       res.redirect('/api/v1/users');
     } else {
       next(); // Proceed to next version
     }
   });
   
   // Final handler for latest version
   app.get('/api/users', (req, res) => {
     res.json([{ id: 1, name: 'Alice', email: 'alice@example.com' }]);
   });
   
   const PORT = process.env.PORT || 3000;
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

- **Maintain legacy routes**: Define routes for old versions.
- **Redirect legacy requests**: Optionally redirect to updated endpoints.
- **Ensure backward compatibility**: Handle old versions while supporting new ones.



## Rate Limiting and Throttling
### 1. Implementing Rate Limits on Endpoints

**Implement rate limiting** to prevent abuse by restricting the number of requests.

**Example: Implementing Rate Limits**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const rateLimit = require('express-rate-limit');
   const app = express();
   app.use(express.json());
   
   // Define rate limit rule
   const limiter = rateLimit({
     windowMs: 15 * 60 * 1000, // 15 minutes
     max: 100, // Limit each IP to 100 requests per windowMs
     message: 'Too many requests from this IP, please try again later.',
   });
   
   // Apply rate limit to all API routes
   app.use('/api/', limiter);
   
   // Example route
   app.get('/api/data', (req, res) => {
     res.json({ data: 'Some data' });
   });
   
   const PORT = process.env.PORT || 3000;
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

- **Use `express-rate-limit`**: Install with `npm install express-rate-limit`.
- **Set rate limit parameters**: Configure `windowMs`, `max`, and `message`.
- **Apply to endpoints**: Use middleware to enforce limits.

### 2. Protecting Against API Abuse

**Protect against API abuse** by implementing rate limiting and monitoring.

**Example: Protecting Against Abuse**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const rateLimit = require('express-rate-limit');
   const app = express();
   app.use(express.json());
   
   // Rate limiter configuration
   const apiLimiter = rateLimit({
     windowMs: 15 * 60 * 1000, // 15 minutes
     max: 100, // Limit each IP to 100 requests per windowMs
     message: 'Too many requests, please try again later.',
   });
   
   // Apply rate limiter to all API routes
   app.use('/api/', apiLimiter);
   
   // Example route
   app.get('/api/data', (req, res) => {
     res.json({ data: 'Protected data' });
   });
   
   // Example of IP blocking
   const blockedIps = new Set();
   app.use((req, res, next) => {
     const ip = req.ip;
     if (blockedIps.has(ip)) {
       return res.status(403).json({ error: 'Access blocked' });
     }
     next();
   });
   
   const PORT = process.env.PORT || 3000;
   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

- **Use `express-rate-limit`** to limit requests.
- **Monitor IP addresses** for excessive requests.
- **Block abusive IPs** by maintaining a blocked list.