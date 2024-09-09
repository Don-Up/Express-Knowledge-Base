# Routing

## Defining Routes
### 1. GET, POST, PUT, DELETE

Define routes in Express for different HTTP methods to handle various types of requests.

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

// Middleware to parse JSON bodies
app.use(express.json());

// GET route
app.get('/items', (req, res) => {
  res.send('GET request to /items');
});

// POST route
app.post('/items', (req, res) => {
  const { item } = req.body;
  res.send(`POST request with item: ${item}`);
});

// PUT route
app.put('/items/:id', (req, res) => {
  const { id } = req.params;
  const { item } = req.body;
  res.send(`PUT request for item ${id} with new data: ${item}`);
});

// DELETE route
app.delete('/items/:id', (req, res) => {
  const { id } = req.params;
  res.send(`DELETE request for item ${id}`);
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **GET**: Retrieve resources.
2. **POST**: Create resources.
3. **PUT**: Update resources.
4. **DELETE**: Remove resources.

This setup defines routes for handling various HTTP methods.

### 2. Handling Different HTTP Methods

Handle different HTTP methods (GET, POST, PUT, DELETE) in Express by defining routes for each method to manage various request types.

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

// Middleware to parse JSON bodies
app.use(express.json());

// Handle GET request
app.get('/api/data', (req, res) => {
  res.send('GET request to /api/data');
});

// Handle POST request
app.post('/api/data', (req, res) => {
  const { newData } = req.body;
  res.send(`POST request with data: ${newData}`);
});

// Handle PUT request
app.put('/api/data/:id', (req, res) => {
  const { id } = req.params;
  const { updatedData } = req.body;
  res.send(`PUT request for ID ${id} with data: ${updatedData}`);
});

// Handle DELETE request
app.delete('/api/data/:id', (req, res) => {
  const { id } = req.params;
  res.send(`DELETE request for ID ${id}`);
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **GET**: Fetch data.
2. **POST**: Add new data.
3. **PUT**: Modify existing data.
4. **DELETE**: Remove data.

This setup demonstrates handling different HTTP methods with specific routes.



## Route Parameters and Query Strings
### 1. Accessing URL Parameters

Access URL parameters and query strings in Express routes to handle dynamic values and filter data.

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

// Middleware to parse JSON bodies
app.use(express.json());

// Route with URL parameters
app.get('/api/items/:id', (req, res) => {
  const { id } = req.params;
  res.send(`Item ID: ${id}`);
});

// Route with query strings
app.get('/api/search', (req, res) => {
  const { query } = req.query;
  res.send(`Search query: ${query}`);
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **URL Parameters**: Use `req.params` to access dynamic segments of the URL (e.g., `/api/items/:id`).
2. **Query Strings**: Use `req.query` to access query string values (e.g., `/api/search?query=value`).

This setup demonstrates accessing and using URL parameters and query strings in Express routes.

### 2. Handling Query Parameters

Handle query parameters in Express routes to filter or manipulate data based on user input.

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

// Middleware to parse JSON bodies
app.use(express.json());

// Route with query parameters
app.get('/api/users', (req, res) => {
  const { age, name } = req.query;
  res.send(`Filtering users by age: ${age} and name: ${name}`);
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Query Parameters**: Access parameters using `req.query` (e.g., `/api/users?age=25&name=John`).

This setup allows handling and using query parameters to filter or modify responses based on the request.



## Route Grouping and Modularity
### 1. Using express.Router

Use `express.Router` to modularize and group routes, improving code organization by separating route logic into different files.

**Demo Code:**

**1. Initialize Project:**

```bash
mkdir my-express-app
cd my-express-app
npm init -y
npm install express
```

**2. Create Main Server File (`server.ts`):**

```typescript
import express from 'express';
import userRouter from './routes/userRoutes'; // Import router module

const app = express();
const port = 3000;

// Middleware to parse JSON bodies
app.use(express.json());

// Use router for /users routes
app.use('/users', userRouter);

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**3. Create Router File (`routes/userRoutes.ts`):**

```typescript
import express from 'express';

const router = express.Router();

// Define routes
router.get('/', (req, res) => {
  res.send('GET request to /users');
});

router.post('/', (req, res) => {
  const { user } = req.body;
  res.send(`POST request with user: ${user}`);
});

export default router;
```

**Usage:**

1. **Create Router**: Use `express.Router()` to define route handlers.
2. **Modularize Routes**: Export router and import it in the main server file.

This setup organizes routes using `express.Router`, promoting modularity and cleaner code structure.

### 2. Organizing Routes in Separate Files

Organize routes by placing them in separate files, enhancing modularity and maintainability.

**Demo Code:**

**1. Initialize Project:**

```bash
mkdir my-express-app
cd my-express-app
npm init -y
npm install express
```

**2. Create Main Server File (`server.ts`):**

```typescript
import express from 'express';
import userRoutes from './routes/userRoutes';
import productRoutes from './routes/productRoutes';

const app = express();
const port = 3000;

// Middleware to parse JSON bodies
app.use(express.json());

// Use route modules
app.use('/users', userRoutes);
app.use('/products', productRoutes);

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**3. Create User Routes File (`routes/userRoutes.ts`):**

```typescript
import express from 'express';

const router = express.Router();

// Define user routes
router.get('/', (req, res) => {
  res.send('GET users');
});

router.post('/', (req, res) => {
  const { user } = req.body;
  res.send(`POST user: ${user}`);
});

export default router;
```

**4. Create Product Routes File (`routes/productRoutes.ts`):**

```typescript
import express from 'express';

const router = express.Router();

// Define product routes
router.get('/', (req, res) => {
  res.send('GET products');
});

router.post('/', (req, res) => {
  const { product } = req.body;
  res.send(`POST product: ${product}`);
});

export default router;
```

**Usage:**

1. **Create Route Files**: Define routes in separate files.
2. **Import and Use**: Import route modules and use them in the main server file.

This setup organizes routes into separate files for better structure and maintainability.