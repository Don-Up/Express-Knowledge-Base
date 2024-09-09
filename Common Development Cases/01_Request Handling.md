# Request Handling

## Handling JSON and URL-encoded Data
### 1. Parsing Request Body

Use middleware to parse JSON and URL-encoded data from request bodies.

**Demo Code:**

```typescript
import express from 'express';

const app = express();
const port = 3000;

// Middleware to parse JSON bodies
app.use(express.json());

// Middleware to parse URL-encoded bodies
app.use(express.urlencoded({ extended: true }));

// Define a route to handle POST requests
app.post('/data', (req, res) => {
  const { jsonData } = req.body; // Extract data from JSON body
  res.send(`Received JSON data: ${jsonData}`);
});

// Define a route to handle URL-encoded data
app.post('/form', (req, res) => {
  const { formData } = req.body; // Extract data from URL-encoded body
  res.send(`Received form data: ${formData}`);
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Parse JSON**: Use `express.json()` for JSON request bodies.
2. **Parse URL-encoded**: Use `express.urlencoded({ extended: true })` for form data.

This setup demonstrates parsing and handling different request body formats in Express.

### 2. Validating Incoming Data

Validate incoming JSON and URL-encoded data using middleware.

**Demo Code:**

```typescript
import express from 'express';
import { body, validationResult } from 'express-validator';

const app = express();
const port = 3000;

// Middleware to parse JSON bodies
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Route with validation for JSON data
app.post('/json', 
  body('jsonData').isString().notEmpty(), // Validation rules
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    res.send(`Valid JSON data: ${req.body.jsonData}`);
  }
);

// Route with validation for URL-encoded data
app.post('/form', 
  body('formData').isString().notEmpty(), // Validation rules
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    res.send(`Valid form data: ${req.body.formData}`);
  }
);

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Install Validator**: Use `express-validator` for validation rules.
2. **Apply Validation**: Define validation rules and handle errors in routes.

This setup demonstrates validating and handling incoming data in Express routes.



## File Uploads

### 1. Using Multer for File Uploads

Use `multer` middleware to handle file uploads in Express.

**Demo Code:**

```typescript
import express from 'express';
import multer from 'multer';

const app = express();
const port = 3000;

// Configure multer for file uploads
const upload = multer({ dest: 'uploads/' }); // Store files in 'uploads' directory

// Route for file upload
app.post('/upload', upload.single('file'), (req, res) => {
  if (!req.file) {
    return res.status(400).send('No file uploaded.');
  }
  res.send(`File uploaded: ${req.file.originalname}`);
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Install Multer**: Use `npm install multer` to handle file uploads.
2. **Configure Middleware**: Use `multer` to specify file storage and handling.

This setup demonstrates handling file uploads with Multer in an Express application.

### 2. Handling File Size Limits and File Types

Use `multer` to set limits on file size and types for file uploads.

**Demo Code:**

```typescript
import express from 'express';
import multer from 'multer';

const app = express();
const port = 3000;

// Configure multer with file size limit and type filter
const upload = multer({
  dest: 'uploads/',
  limits: { fileSize: 1 * 1024 * 1024 }, // 1 MB file size limit
  fileFilter: (req, file, cb) => {
    if (!file.mimetype.startsWith('image/')) {
      return cb(new Error('Only image files are allowed'), false);
    }
    cb(null, true);
  }
});

// Route for file upload
app.post('/upload', upload.single('file'), (req, res) => {
  if (!req.file) {
    return res.status(400).send('File is required.');
  }
  res.send(`File uploaded: ${req.file.originalname}`);
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Set Size Limit**: Use `limits.fileSize` to restrict file size.
2. **Filter File Types**: Use `fileFilter` to restrict file types.

This setup demonstrates handling file size limits and type restrictions using Multer in an Express application.



## Sending Responses

### 1.  JSON, HTML, Files

Send different types of responses from Express routes: JSON, HTML, and files.

**Demo Code:**

```typescript
import express from 'express';
import path from 'path';

const app = express();
const port = 3000;

// Route for sending JSON response
app.get('/json', (req, res) => {
  res.json({ message: 'This is a JSON response' });
});

// Route for sending HTML response
app.get('/html', (req, res) => {
  res.send('<h1>This is an HTML response</h1>');
});

// Route for sending a file
app.get('/file', (req, res) => {
  res.sendFile(path.join(__dirname, 'file.txt')); // Adjust the file path as needed
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Send JSON**: Use `res.json()` for JSON responses.
2. **Send HTML**: Use `res.send()` for HTML content.
3. **Send Files**: Use `res.sendFile()` to serve files.

This setup demonstrates how to send JSON, HTML, and files as responses in an Express application.

### 2.  Setting Status Codes

Set HTTP status codes to indicate the result of a request.

**Demo Code:**

```typescript
import express from 'express';

const app = express();
const port = 3000;

// Route for successful response
app.get('/success', (req, res) => {
  res.status(200).json({ message: 'Request was successful' });
});

// Route for client error response
app.get('/error', (req, res) => {
  res.status(400).json({ error: 'Bad request' });
});

// Route for server error response
app.get('/server-error', (req, res) => {
  res.status(500).json({ error: 'Internal server error' });
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Set Status Code**: Use `res.status(code)` to set the HTTP status code.
2. **Send Response**: Chain with `res.json()` or `res.send()` to send the response.

This setup demonstrates setting and sending HTTP status codes for different types of responses in an Express application.