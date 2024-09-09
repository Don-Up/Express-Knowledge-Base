# Session Management

## Using Sessions
### 1. Storing Session Data

Store session data in Express.js using `express-session`.

**Demo Code:**

```typescript
import express from 'express';
import session from 'express-session';

const app = express();
const port = 3000;

app.use(express.json());

// Session configuration
app.use(session({
  secret: 'your-secret-key',  // Secret key for signing the session ID cookie
  resave: false,              // Whether to save the session back to the store even if it was not modified
  saveUninitialized: true,    // Save a session that is new but not modified
  cookie: { secure: false }   // Set secure to true in production with HTTPS
}));

// Store session data
app.post('/login', (req, res) => {
  // Example: Store user info in session
  req.session.user = { id: 1, name: 'John Doe' };
  res.send('User logged in and session data stored');
});

// Access session data
app.get('/profile', (req, res) => {
  if (req.session.user) {
    res.json(req.session.user);
  } else {
    res.status(401).send('Unauthorized');
  }
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Storing Data**: Use `req.session` to store session data.
2. **Accessing Data**: Retrieve session data from `req.session` in other routes.

This setup demonstrates basic session management, including storing and accessing session data in an Express application.

### 2. Session Persistence with Store (Redis, MongoDB)

Persist sessions using `connect-mongo` with MongoDB in Express.js.

**Demo Code:**

```typescript
import express from 'express';
import session from 'express-session';
import MongoStore from 'connect-mongo';
import mongoose from 'mongoose';

const app = express();
const port = 3000;

// MongoDB connection
mongoose.connect('mongodb://localhost:27017/sessions', { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('Connected to MongoDB'))
  .catch(error => console.error('MongoDB connection error:', error.message));

// Session configuration
app.use(session({
  secret: 'your-secret-key',
  resave: false,
  saveUninitialized: true,
  store: MongoStore.create({ mongoUrl: 'mongodb://localhost:27017/sessions' }), // Use MongoDB store
  cookie: { secure: false }  // Set secure to true in production with HTTPS
}));

// Store session data
app.post('/login', (req, res) => {
  req.session.user = { id: 1, name: 'John Doe' };
  res.send('User logged in and session data stored');
});

// Access session data
app.get('/profile', (req, res) => {
  if (req.session.user) {
    res.json(req.session.user);
  } else {
    res.status(401).send('Unauthorized');
  }
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Session Store**: Use `MongoStore.create` to configure session storage in MongoDB.
2. **Persistence**: Sessions persist across server restarts by storing data in MongoDB.

This setup demonstrates how to persist sessions using MongoDB with `connect-mongo` in an Express application.



## Cookie Management
### 1. Setting and Reading Cookies

Manage cookies using `cookie-parser` in Express.js.

**Demo Code:**

```typescript
import express from 'express';
import cookieParser from 'cookie-parser';

const app = express();
const port = 3000;

app.use(cookieParser('your-secret-key')); // Initialize cookie-parser with a secret

// Set a cookie
app.get('/set-cookie', (req, res) => {
  res.cookie('username', 'JohnDoe', { httpOnly: true, maxAge: 3600000 }); // Cookie expires in 1 hour
  res.send('Cookie has been set');
});

// Read cookies
app.get('/get-cookie', (req, res) => {
  const username = req.cookies['username'];
  if (username) {
    res.send(`Cookie value: ${username}`);
  } else {
    res.send('No cookie found');
  }
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Setting Cookies**: Use `res.cookie()` to set cookies with options like `httpOnly` and `maxAge`.
2. **Reading Cookies**: Access cookies from `req.cookies`.

This setup demonstrates setting and reading cookies in an Express application using `cookie-parser`.

### 2. Secure Cookies (HTTPOnly, Secure Flags)

Enhance cookie security with HTTPOnly and Secure flags.

**Demo Code:**

```typescript
import express from 'express';
import cookieParser from 'cookie-parser';

const app = express();
const port = 3000;

app.use(cookieParser('your-secret-key'));

// Set a secure cookie
app.get('/set-secure-cookie', (req, res) => {
  res.cookie('sessionId', 'abc123', {
    httpOnly: true,  // Prevent JavaScript access
    secure: true,    // Only sent over HTTPS
    maxAge: 3600000  // Cookie expires in 1 hour
  });
  res.send('Secure cookie has been set');
});

// Read cookies
app.get('/get-cookie', (req, res) => {
  const sessionId = req.cookies['sessionId'];
  if (sessionId) {
    res.send(`Cookie value: ${sessionId}`);
  } else {
    res.send('No cookie found');
  }
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **HTTPOnly Flag**: Use `httpOnly: true` to prevent JavaScript access.
2. **Secure Flag**: Use `secure: true` to ensure cookies are sent only over HTTPS.

This setup demonstrates setting and securing cookies with HTTPOnly and Secure flags in an Express application.