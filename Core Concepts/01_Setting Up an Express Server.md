# Setting Up an Express Server

## Basic Server Setup

**Express.js - Setting Up an Express Server: Basic Server Setup**

Create a basic Express server by initializing a new project, installing Express, and setting up a simple server with a route.

**Demo Code:**

**1. Initialize Project:**

```bash
mkdir my-express-app
cd my-express-app
npm init -y
npm install express
```

**2. Create Server File (`server.ts`):**

```typescript
import express from 'express';

const app = express();
const port = 3000;

// Define a basic route
app.get('/', (req, res) => {
  res.send('Hello, World!');
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Install Express**: Run `npm install express`.
2. **Create Server**: Define routes and start the server with `app.listen()`.

This sets up a basic Express server that responds with "Hello, World!" on the root route.



## Creating a Simple Server
### Listening on a Port

Create an Express server that listens on a specified port and responds to HTTP requests. Define routes and start the server to listen for incoming connections.

**Demo Code:**

**1. Initialize Project:**

```bash
mkdir my-express-app
cd my-express-app
npm init -y
npm install express
```

**2. Create Server File (`server.ts`):**

```typescript
import express from 'express';

const app = express();
const port = 4000; // Define the port number

// Define a route
app.get('/', (req, res) => {
  res.send('Hello from Express!');
});

// Start the server
app.listen(port, () => {
  console.log(`Server is listening on http://localhost:${port}`);
});
```

**Usage:**

1. **Install Dependencies**: Run `npm install express`.
2. **Create Server**: Use `app.listen(port)` to start the server on the specified port.

This example sets up a simple server that listens on port 4000 and responds with "Hello from Express!" on the root route.



## Middleware Functions
### 1. Application-Level Middleware

Application-level middleware functions are used to handle requests and responses at the application level. They can be used for tasks like logging, authentication, and parsing request bodies.

**Demo Code:**

**1. Initialize Project:**

```bash
mkdir my-express-app
cd my-express-app
npm init -y
npm install express
```

**2. Create Server File (`server.ts`):**

```typescript
import express from 'express';

const app = express();
const port = 3000;

// Application-level middleware to log requests
app.use((req, res, next) => {
  console.log(`Received request for ${req.url}`);
  next(); // Pass control to the next middleware
});

// Middleware to parse JSON bodies
app.use(express.json());

// Define a route
app.post('/data', (req, res) => {
  res.send(`Received data: ${JSON.stringify(req.body)}`);
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Add Middleware**: Use `app.use()` to apply middleware functions.
2. **Handle Requests**: Middleware processes requests before reaching routes.

This setup includes logging middleware and JSON body parsing middleware for handling incoming requests.

### 2. Router-Level Middleware

Router-level middleware is used for handling requests at a more granular level, specific to individual routers. This allows middleware to be applied only to routes defined in that router.

**Demo Code:**

**1. Initialize Project:**

```bash
mkdir my-express-app
cd my-express-app
npm init -y
npm install express
```

**2. Create Server File (`server.ts`):**

```typescript
import express from 'express';

const app = express();
const port = 3000;

// Create a router
const router = express.Router();

// Router-level middleware to log requests
router.use((req, res, next) => {
  console.log(`Router received request for ${req.url}`);
  next();
});

// Define routes in the router
router.get('/items', (req, res) => {
  res.send('Items list');
});

// Apply router-level middleware and routes
app.use('/api', router);

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Define Router**: Use `express.Router()` to create a router.
2. **Apply Middleware**: Use `router.use()` to add middleware for specific routes.
3. **Mount Router**: Use `app.use('/api', router)` to mount the router.

This setup applies router-level middleware to the `/api` routes, logging requests specific to those routes.

### 3. Error-Handling Middleware

Error-handling middleware in Express captures and manages errors, providing a consistent response for errors across the application.

**Demo Code:**

**1. Initialize Project:**

```bash
mkdir my-express-app
cd my-express-app
npm init -y
npm install express
```

**2. Create Server File (`server.ts`):**

```typescript
import express from 'express';

const app = express();
const port = 3000;

// Middleware to handle JSON requests
app.use(express.json());

// Define a route that causes an error
app.get('/', (req, res) => {
  throw new Error('Something went wrong!');
});

// Error-handling middleware
app.use((err: Error, req: express.Request, res: express.Response, next: express.NextFunction) => {
  console.error(err.stack); // Log error stack
  res.status(500).send('Something broke!');
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Define Error-Handling Middleware**: Use `app.use()` with an error-handling function.
2. **Handle Errors**: Capture errors and send a consistent response.

This setup includes an error-handling middleware to manage errors and respond with a 500 status and message.

### 4. Built-in Middleware (express.json, express.urlencoded)

Express provides built-in middleware for parsing JSON and URL-encoded request bodies, simplifying data handling in routes.

**Demo Code:**

**1. Initialize Project:**

```bash
mkdir my-express-app
cd my-express-app
npm init -y
npm install express
```

**2. Create Server File (`server.ts`):**

```typescript
import express from 'express';

const app = express();
const port = 3000;

// Built-in middleware to parse JSON bodies
app.use(express.json());

// Built-in middleware to parse URL-encoded bodies
app.use(express.urlencoded({ extended: true }));

// Define a route that uses the parsed body data
app.post('/submit', (req, res) => {
  const { name, age } = req.body;
  res.send(`Received: Name=${name}, Age=${age}`);
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Parse JSON**: Use `express.json()` to handle JSON bodies.
2. **Parse URL-encoded**: Use `express.urlencoded({ extended: true })` for form data.

This setup uses built-in middleware to parse and handle request bodies efficiently.

