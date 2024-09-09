# Custom Middleware Development

## Writing Reusable Middleware
### Handling Requests and Responses

**Create custom middleware** in Express to handle requests and responses efficiently.

**Example: Custom Middleware**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();
   const PORT = process.env.PORT || 3000;

   // Custom middleware to log request details
   const requestLogger = (req, res, next) => {
     console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
     next(); // Pass control to the next middleware
   };

   // Custom middleware to add a custom header
   const addCustomHeader = (req, res, next) => {
     res.setHeader('X-Custom-Header', 'CustomHeaderValue');
     next(); // Pass control to the next middleware
   };

   // Use custom middleware
   app.use(requestLogger);
   app.use(addCustomHeader);

   app.get('/', (req, res) => {
     res.send('Custom middleware in action!');
   });

   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **Middleware Functions**:

   - **`requestLogger`**: Logs request method and URL.
   - **`addCustomHeader`**: Adds a custom header to the response.

- **Write middleware** to handle specific tasks.
- **Use `app.use()`** to apply middleware to routes.
- **Ensure proper order** of middleware execution.



## Middleware for Specific Use Cases
### Authentication, Logging, Error Tracking

**Develop middleware** for tasks like authentication, logging, and error tracking.

**Example: Custom Middleware**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();
   const PORT = process.env.PORT || 3000;

   // Authentication middleware
   const authenticate = (req, res, next) => {
     if (req.headers.authorization === 'Bearer secret-token') {
       next(); // User authenticated
     } else {
       res.status(401).send('Unauthorized');
     }
   };

   // Logging middleware
   const logRequests = (req, res, next) => {
     console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
     next();
   };

   // Error tracking middleware
   const errorTracker = (err, req, res, next) => {
     console.error(`Error: ${err.message}`);
     res.status(500).send('Internal Server Error');
   };

   // Use middleware
   app.use(logRequests);
   app.use('/secure', authenticate); // Apply authentication only to secure routes
   app.use(errorTracker); // Error tracking middleware should be last

   app.get('/', (req, res) => {
     res.send('Public Route');
   });

   app.get('/secure', (req, res) => {
     res.send('Secure Route');
   });

   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **Middleware Functions**:

   - **`authenticate`**: Validates user credentials.
   - **`logRequests`**: Logs request details.
   - **`errorTracker`**: Catches and logs errors.

- **Use `app.use()`** for applying middleware globally or to specific routes.
- **Ensure proper order**: authentication before secure routes, error tracking last.