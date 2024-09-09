# Error Handling

## Handling Errors in Routes
### 1. Using Try-Catch for Async Errors

Use try-catch blocks to handle errors in asynchronous routes.

**Demo Code:**

**1. Create Server File (`server.ts`):**

```ts
import express, { Request, Response, NextFunction } from 'express';

const app = express();
const port = 3000;

// Async route with error handling using try-catch
app.get('/data', async (req: Request, res: Response, next: NextFunction) => {
  try {
    // Simulate an async operation that may fail
    const data = await fetchData(); // Assume fetchData is a function that fetches data
    res.json(data);
  } catch (error) {
    next(error); // Pass the error to the next middleware (error handler)
  }
});

// Centralized error handler
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  console.error(err.message); // Log the error for debugging
  res.status(500).json({ error: 'Internal Server Error' }); // Send a generic error response
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});

// Mock async function that throws an error
async function fetchData() {
  throw new Error('Failed to fetch data');
}
```

**Usage:**

1. **Try-Catch Blocks**: Use try-catch around async code to capture errors.
2. **Error Propagation**: Use `next(error)` to pass errors to the centralized error handler.

This setup demonstrates handling errors in asynchronous routes with try-catch blocks in Express.js, ensuring errors are caught and properly managed.

### 2. Propagating Errors to Error Middleware

Errors in routes can be propagated to error-handling middleware by passing the error to the `next()` function.

**Demo Code:**

**1. Create Server File (`server.ts`):**

```ts
import express, { Request, Response, NextFunction } from 'express';

const app = express();
const port = 3000;

// Route with error propagation
app.get('/users', (req: Request, res: Response, next: NextFunction) => {
  try {
    // Simulate an error
    throw new Error('Unable to fetch users');
  } catch (error) {
    next(error); // Propagate error to error-handling middleware
  }
});

// Centralized error-handling middleware
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  console.error('Error occurred:', err.message); // Log the error
  res.status(500).json({ error: err.message }); // Send error response
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Key Points:**

1. **Error Propagation**: Use `next(error)` within try-catch blocks in routes to pass errors to error-handling middleware.
2. **Error Middleware**: Centralized middleware catches and handles all errors, providing a consistent error response.

This setup demonstrates propagating route errors to a centralized error-handling middleware in Express.js.



## Centralized Error Handling
### 1. Creating a Global Error Handler

Centralized error handling in Express.js involves creating a global error-handling middleware that captures errors from all routes and middleware.

**Demo Code:**

```ts
import express, { Request, Response, NextFunction } from 'express';

const app = express();
const port = 3000;

// Example route that may throw an error
app.get('/data', (req: Request, res: Response, next: NextFunction) => {
  try {
    // Simulate an error
    throw new Error('Data fetch failed');
  } catch (error) {
    next(error); // Pass error to the global error handler
  }
});

// Global Error Handler
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  console.error(`Error: ${err.message}`); // Log the error details

  res.status(500).json({
    status: 'error',
    message: err.message || 'Internal Server Error', // Send a response with the error message
  });
});

// Start the server
app.listen(port, () => {
  console.log(`Server is running on http://localhost:${port}`);
});
```

**Key Points:**

1. **Global Error Handler**: Place the error-handling middleware after all other routes and middleware. It captures errors passed using `next(error)`.
2. **Consistent Error Responses**: Provides a uniform/ˈjuːnɪfɔːrm/ error response structure for the application.

This approach centralizes error management, making it easier to maintain and scale error handling across the entire Express application.

### 2. Custom Error Classes

Using custom error classes in Express.js allows for more descriptive and specific error handling. These classes can extend the built-in `Error` class to add custom properties and methods, aiding centralized error handling.

**Demo Code:**

**1. Create Custom Error Class (`errors/HttpError.ts`):**

```ts
export class HttpError extends Error {
  statusCode: number;

  constructor(message: string, statusCode: number) {
    super(message); // Call the base class constructor
    this.statusCode = statusCode; // Assign a custom status code
    Error.captureStackTrace(this, this.constructor); // Capture the error stack trace
  }
}
```

**2. Implement Centralized Error Handler in Server File (`server.ts`):**

```ts
import express, { Request, Response, NextFunction } from 'express';
import { HttpError } from './errors/HttpError';

const app = express();
const port = 3000;

// Route that throws a custom error
app.get('/data', (req: Request, res: Response, next: NextFunction) => {
  try {
    // Simulate a not found error
    throw new HttpError('Data not found', 404);
  } catch (error) {
    next(error); // Forward error to the global error handler
  }
});

// Global Error Handler
app.use((err: HttpError, req: Request, res: Response, next: NextFunction) => {
  console.error(`Error: ${err.message}`); // Log error details

  res.status(err.statusCode || 500).json({
    status: 'error',
    message: err.message || 'Internal Server Error', // Respond with error details
  });
});

// Start the server
app.listen(port, () => {
  console.log(`Server is running on http://localhost:${port}`);
});
```

**Key Points:**

1. **Custom Error Classes**: Extend the `Error` class to include additional properties like `statusCode`.
2. **Error Handling Middleware**: Use a centralized middleware to handle all errors and send a consistent response based on the error type.

This setup makes error handling more modular and maintains clear separation of concerns, enhancing the robustness of the application.



## Logging Errors
### 1. Using Winston for Error Logging

Winston is a popular logging library in Express.js that helps in logging errors and other application events systematically. It supports multiple transports for log messages, such as files, databases, or the console.

**Demo Code:**

**1. Set Up Winston Logger (`logger.ts`):**

```ts
import winston from 'winston';

// Configure Winston Logger
const logger = winston.createLogger({
  level: 'error', // Set the log level
  format: winston.format.combine(
    winston.format.timestamp(), // Add timestamps to logs
    winston.format.printf(({ timestamp, level, message }) => {
      return `${timestamp} [${level.toUpperCase()}]: ${message}`; // Custom log format
    })
  ),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }), // Log errors to a file
    new winston.transports.Console(), // Also log to the console
  ],
});

export default logger;
```

**2. Use Winston Logger in Error Handling Middleware (`server.ts`):**

```ts
import express, { Request, Response, NextFunction } from 'express';
import { HttpError } from './errors/HttpError';
import logger from './logger';

const app = express();
const port = 3000;

// Example route that triggers an error
app.get('/error', (req: Request, res: Response, next: NextFunction) => {
  try {
    throw new HttpError('Something went wrong!', 500); // Simulate an error
  } catch (error) {
    next(error); // Forward the error to the error handler
  }
});

// Global Error Handler
app.use((err: HttpError, req: Request, res: Response, next: NextFunction) => {
  logger.error(`${err.statusCode || 500} - ${err.message}`); // Log the error using Winston

  res.status(err.statusCode || 500).json({
    status: 'error',
    message: err.message || 'Internal Server Error', // Send error response
  });
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Key Points:**

1. **Winston Logger**: Configured to log error messages with timestamps to both a file and the console.
2. **Error Logging**: Logs errors in a structured format using Winston, which aids in debugging and tracking issues in production.

Using Winston helps maintain a comprehensive log of errors, enhancing the observability and maintainability of the Express.js application.

### 2. Logging to Files and External Services

Logging errors to files and external services in Express.js helps track issues and monitor application health. Using Winston, you can log errors to files and integrate with external services like Loggly or Papertrail for remote error monitoring.

**Demo Code:**

**1. Configure Winston for File and External Service Logging (`logger.ts`):**

```ts
import winston from 'winston';
import 'winston-loggly-bulk'; // For logging to Loggly (or other similar services)

// Configure Winston Logger
const logger = winston.createLogger({
  level: 'error',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json() // JSON format for structured logs
  ),
  transports: [
    new winston.transports.File({ filename: 'errors.log' }), // Log errors to a local file
    // Log errors to an external service like Loggly
    new winston.transports.Loggly({
      token: 'your-loggly-token', // Replace with your Loggly token
      subdomain: 'your-subdomain', // Replace with your Loggly subdomain
      tags: ['express', 'error'],
      json: true,
    }),
  ],
});

export default logger;
```

**2. Use Winston Logger in Error Handling Middleware (`server.ts`):**

```ts
import express, { Request, Response, NextFunction } from 'express';
import { HttpError } from './errors/HttpError';
import logger from './logger';

const app = express();
const port = 3000;

// Example route that triggers an error
app.get('/error', (req: Request, res: Response, next: NextFunction) => {
  try {
    throw new HttpError('An unexpected error occurred!', 500); // Simulate an error
  } catch (error) {
    next(error); // Forward the error to the error handler
  }
});

// Global Error Handler
app.use((err: HttpError, req: Request, res: Response, next: NextFunction) => {
  logger.error({
    message: err.message,
    statusCode: err.statusCode || 500,
    stack: err.stack, // Include stack trace for debugging
  }); // Log the error to file and external service

  res.status(err.statusCode || 500).json({
    status: 'error',
    message: err.message || 'Internal Server Error',
  });
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Key Points:**

1. **File Logging**: Errors are logged to a local file (`errors.log`), which is useful for local debugging.
2. **External Service Logging**: Errors are sent to an external service (e.g., Loggly) for centralized error tracking and alerting.
3. **Structured Logs**: JSON format is used for structured logging, making it easier to parse and analyze logs.

Logging errors both locally and externally helps maintain a robust error monitoring strategy, allowing timely responses to issues in production environments.