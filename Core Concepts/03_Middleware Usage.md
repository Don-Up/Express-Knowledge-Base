# Middleware Usage

## Application-Level Middleware
### 1. Logging Requests

Use application-level middleware to log requests, tracking details for debugging and monitoring.

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
  console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
  next();
});

// Define a route
app.get('/', (req, res) => {
  res.send('Hello, world!');
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Define Middleware**: Use `app.use()` to apply middleware across all routes.
2. **Log Requests**: Capture and log request details such as method and URL.

This setup demonstrates logging HTTP requests using application-level middleware for monitoring and debugging.

### 2. Handling CORS

Use application-level middleware to handle Cross-Origin Resource Sharing (CORS), allowing or restricting resource sharing across different domains.

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

// Application-level middleware to handle CORS
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*'); // Allow all origins
  res.header('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE'); // Allowed methods
  res.header('Access-Control-Allow-Headers', 'Content-Type'); // Allowed headers
  next();
});

// Define a route
app.get('/', (req, res) => {
  res.send('CORS handled!');
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Add CORS Headers**: Use `res.header()` to set CORS-related headers.
2. **Allow Origins/Methods**: Specify allowed origins, methods, and headers.

This setup demonstrates handling CORS using application-level middleware to control cross-origin resource access.

### 3. Implementing Body Parsing

Use application-level middleware to parse incoming request bodies in different formats (e.g., JSON, URL-encoded).

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

// Middleware to parse URL-encoded bodies
app.use(express.urlencoded({ extended: true }));

// Define a route
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

1. **Parse JSON**: Use `express.json()` to handle JSON request bodies.
2. **Parse URL-encoded**: Use `express.urlencoded({ extended: true })` for form data.

This setup demonstrates implementing body parsing middleware to handle various request body formats.



## Error-Handling Middleware
### 1. Centralized Error Handling

Implement centralized error-handling middleware in Express to manage errors uniformly across the application.

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

// Example route
app.get('/', (req, res) => {
  throw new Error('Something went wrong!');
});

// Centralized error-handling middleware
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

1. **Throw Errors**: Generate errors in routes or middleware.
2. **Handle Errors**: Use `app.use()` with four parameters for centralized error handling.

This setup demonstrates centralized error handling in Express for consistent error management.

### 2. Custom Error Messages

Implement custom error messages in Express middleware to provide user-friendly error responses.

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

// Example route
app.get('/', (req, res) => {
  throw new CustomError('Custom error occurred', 400);
});

// Custom error class
class CustomError extends Error {
  statusCode: number;
  constructor(message: string, statusCode: number) {
    super(message);
    this.statusCode = statusCode;
  }
}

// Custom error-handling middleware
app.use((err: CustomError, req: express.Request, res: express.Response, next: express.NextFunction) => {
  res.status(err.statusCode || 500).json({
    message: err.message || 'An unexpected error occurred',
  });
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Create Custom Error**: Define a custom error class with status codes.
2. **Handle Errors**: Use error-handling middleware to send custom messages.

This setup demonstrates handling and customizing error responses in Express applications.



## Third-Party Middleware
### 1. Using Helmet for Security

Use the `helmet` middleware to enhance security by setting various HTTP headers.

**Demo Code:**

**1. Initialize Project:**

```bash
mkdir my-express-app
cd my-express-app
npm init -y
npm install express helmet
```

**2. Create Server File (`server.ts`):**

```typescript
import express from 'express';
import helmet from 'helmet';

const app = express();
const port = 3000;

// Use Helmet for security
app.use(helmet());

// Define a route
app.get('/', (req, res) => {
  res.send('Security enhanced with Helmet!');
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Install Helmet**: Use `npm install helmet`.
2. **Apply Middleware**: Use `app.use(helmet())` to set security headers.

This setup demonstrates integrating Helmet to improve application security by setting HTTP headers.

### 2. Morgan for Logging

Use the `morgan` middleware to log HTTP requests in your Express application.

**Demo Code:**

**1. Initialize Project:**

```bash
mkdir my-express-app
cd my-express-app
npm init -y
npm install express morgan
```

**2. Create Server File (`server.ts`):**

```typescript
import express from 'express';
import morgan from 'morgan';

const app = express();
const port = 3000;

// Use Morgan for request logging
app.use(morgan('combined')); // 'combined' format provides detailed logs

// Define a route
app.get('/', (req, res) => {
  res.send('Logging requests with Morgan!');
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Install Morgan**: Use `npm install morgan`.
2. **Apply Middleware**: Use `app.use(morgan('combined'))` for logging requests.

This setup demonstrates using Morgan to log HTTP requests, aiding in monitoring and debugging.