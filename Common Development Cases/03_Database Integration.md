# Database Integration

## Connecting to Databases

#### 1. Using Mongoose for MongoDB

Connect to MongoDB using Mongoose and perform basic operations.

**Demo Code:**

```typescript
import express from 'express';
import mongoose from 'mongoose';
import bodyParser from 'body-parser';

const app = express();
const port = 3000;

app.use(bodyParser.json());

// MongoDB connection
mongoose.connect('mongodb://localhost:27017/mydatabase', { useNewUrlParser: true, useUnifiedTopology: true });

const db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', () => {
  console.log('Connected to MongoDB');
});

// Define a schema and model
const userSchema = new mongoose.Schema({
  username: String,
  password: String
});

const User = mongoose.model('User', userSchema);

// Route to create a new user
app.post('/users', async (req, res) => {
  try {
    const newUser = new User(req.body);
    const savedUser = await newUser.save();
    res.status(201).json(savedUser);
  } catch (error) {
    res.status(400).send(error.message);
  }
});

// Route to get all users
app.get('/users', async (req, res) => {
  try {
    const users = await User.find();
    res.json(users);
  } catch (error) {
    res.status(500).send(error.message);
  }
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Install Mongoose**: Use `npm install mongoose`.
2. **Connect to MongoDB**: Use `mongoose.connect()` to connect to the database.
3. **Define Schema and Model**: Create schemas and models for database operations.
4. **Perform CRUD Operations**: Use Mongoose methods to create and retrieve data.

This setup demonstrates connecting to MongoDB using Mongoose and performing basic database operations in an Express application.

#### 2. Integrating with SQL Databases (Sequelize)

Connect to SQL databases using Sequelize ORM and perform basic operations.

**Demo Code:**

```typescript
import express from 'express';
import { Sequelize, DataTypes } from 'sequelize';
import bodyParser from 'body-parser';

const app = express();
const port = 3000;

app.use(bodyParser.json());

// SQL database connection
const sequelize = new Sequelize('mysql://user:pass@localhost:3306/mydatabase');

// Define a model
const User = sequelize.define('User', {
  username: {
    type: DataTypes.STRING,
    allowNull: false
  },
  password: {
    type: DataTypes.STRING,
    allowNull: false
  }
});

// Sync models with the database
sequelize.sync();

// Route to create a new user
app.post('/users', async (req, res) => {
  try {
    const newUser = await User.create(req.body);
    res.status(201).json(newUser);
  } catch (error) {
    res.status(400).send(error.message);
  }
});

// Route to get all users
app.get('/users', async (req, res) => {
  try {
    const users = await User.findAll();
    res.json(users);
  } catch (error) {
    res.status(500).send(error.message);
  }
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Install Sequelize**: Use `npm install sequelize mysql2` (or another SQL dialect).
2. **Connect to SQL Database**: Use `new Sequelize()` to connect.
3. **Define Models**: Create models with `sequelize.define()`.
4. **Perform CRUD Operations**: Use Sequelize methods to interact with the database.

This setup demonstrates connecting to a SQL database using Sequelize ORM and performing basic CRUD operations in an Express application.



## CRUD Operations

 ### 1. Creating, Reading, Updating, Deleting Records

Perform CRUD operations on MongoDB using Mongoose.

**Demo Code:**

```typescript
import express from 'express';
import mongoose from 'mongoose';
import bodyParser from 'body-parser';

const app = express();
const port = 3000;

app.use(bodyParser.json());

// MongoDB connection
mongoose.connect('mongodb://localhost:27017/mydatabase', { useNewUrlParser: true, useUnifiedTopology: true });

const db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', () => {
  console.log('Connected to MongoDB');
});

// Define a schema and model
const userSchema = new mongoose.Schema({
  username: { type: String, required: true },
  password: { type: String, required: true }
});

const User = mongoose.model('User', userSchema);

// Create a new user
app.post('/users', async (req, res) => {
  try {
    const newUser = new User(req.body);
    const savedUser = await newUser.save();
    res.status(201).json(savedUser);
  } catch (error) {
    res.status(400).send(error.message);
  }
});

// Read all users
app.get('/users', async (req, res) => {
  try {
    const users = await User.find();
    res.json(users);
  } catch (error) {
    res.status(500).send(error.message);
  }
});

// Update a user by ID
app.put('/users/:id', async (req, res) => {
  try {
    const updatedUser = await User.findByIdAndUpdate(req.params.id, req.body, { new: true });
    if (updatedUser) {
      res.json(updatedUser);
    } else {
      res.status(404).send('User not found');
    }
  } catch (error) {
    res.status(400).send(error.message);
  }
});

// Delete a user by ID
app.delete('/users/:id', async (req, res) => {
  try {
    const deletedUser = await User.findByIdAndDelete(req.params.id);
    if (deletedUser) {
      res.status(204).send();
    } else {
      res.status(404).send('User not found');
    }
  } catch (error) {
    res.status(500).send(error.message);
  }
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Create Record**: Use `new User(req.body).save()` to add new records.
2. **Read Records**: Use `User.find()` to fetch records.
3. **Update Record**: Use `User.findByIdAndUpdate()` to modify existing records.
4. **Delete Record**: Use `User.findByIdAndDelete()` to remove records.

This setup demonstrates performing CRUD operations with Mongoose in an Express application connected to a MongoDB database.

***

Perform CRUD operations on SQL database using Sequelize ORM.

**Demo Code:**

```typescript
import express from 'express';
import { Sequelize, DataTypes } from 'sequelize';
import bodyParser from 'body-parser';

const app = express();
const port = 3000;

app.use(bodyParser.json());

// SQL database connection
const sequelize = new Sequelize('mysql://user:pass@localhost:3306/mydatabase');

// Define a model
const User = sequelize.define('User', {
  username: {
    type: DataTypes.STRING,
    allowNull: false
  },
  password: {
    type: DataTypes.STRING,
    allowNull: false
  }
});

// Sync models with the database
sequelize.sync();

// Create a new user
app.post('/users', async (req, res) => {
  try {
    const newUser = await User.create(req.body);
    res.status(201).json(newUser);
  } catch (error) {
    res.status(400).send(error.message);
  }
});

// Read all users
app.get('/users', async (req, res) => {
  try {
    const users = await User.findAll();
    res.json(users);
  } catch (error) {
    res.status(500).send(error.message);
  }
});

// Update a user by ID
app.put('/users/:id', async (req, res) => {
  try {
    const [updated] = await User.update(req.body, { where: { id: req.params.id } });
    if (updated) {
      const updatedUser = await User.findByPk(req.params.id);
      res.json(updatedUser);
    } else {
      res.status(404).send('User not found');
    }
  } catch (error) {
    res.status(400).send(error.message);
  }
});

// Delete a user by ID
app.delete('/users/:id', async (req, res) => {
  try {
    const deleted = await User.destroy({ where: { id: req.params.id } });
    if (deleted) {
      res.status(204).send();
    } else {
      res.status(404).send('User not found');
    }
  } catch (error) {
    res.status(500).send(error.message);
  }
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Create Record**: Use `User.create()` to add new records.
2. **Read Records**: Use `User.findAll()` to fetch records.
3. **Update Record**: Use `User.update()` to modify existing records.
4. **Delete Record**: Use `User.destroy()` to remove records.

This setup demonstrates performing CRUD operations with Sequelize in an Express application.

### 2. Handling Relationships (One-to-Many, Many-to-Many)

**Express.js - Database Integration: CRUD Operations - Handling Relationships (One-to-Many, Many-to-Many)**

Handle one-to-many and many-to-many relationships using Mongoose in Express.js.

**Demo Code:**

**1. Create Server File (`server.ts`):**

```typescript
import express from 'express';
import mongoose from 'mongoose';
import bodyParser from 'body-parser';

const app = express();
const port = 3000;

app.use(bodyParser.json());

// MongoDB connection
mongoose.connect('mongodb://localhost:27017/mydatabase', { useNewUrlParser: true, useUnifiedTopology: true });

const db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', () => {
  console.log('Connected to MongoDB');
});

// Define schemas and models

// One-to-Many: Author and Books
const authorSchema = new mongoose.Schema({
  name: { type: String, required: true }
});

const bookSchema = new mongoose.Schema({
  title: { type: String, required: true },
  author: { type: mongoose.Schema.Types.ObjectId, ref: 'Author' }
});

const Author = mongoose.model('Author', authorSchema);
const Book = mongoose.model('Book', bookSchema);

// Many-to-Many: Students and Courses
const studentSchema = new mongoose.Schema({
  name: { type: String, required: true },
  courses: [{ type: mongoose.Schema.Types.ObjectId, ref: 'Course' }]
});

const courseSchema = new mongoose.Schema({
  title: { type: String, required: true },
  students: [{ type: mongoose.Schema.Types.ObjectId, ref: 'Student' }]
});

const Student = mongoose.model('Student', studentSchema);
const Course = mongoose.model('Course', courseSchema);

// Create an author and a book
app.post('/authors', async (req, res) => {
  try {
    const newAuthor = new Author(req.body);
    const savedAuthor = await newAuthor.save();
    res.status(201).json(savedAuthor);
  } catch (error) {
    res.status(400).send(error.message);
  }
});

app.post('/books', async (req, res) => {
  try {
    const newBook = new Book(req.body);
    const savedBook = await newBook.save();
    res.status(201).json(savedBook);
  } catch (error) {
    res.status(400).send(error.message);
  }
});

// Link a book to an author
app.put('/books/:id/author', async (req, res) => {
  try {
    const book = await Book.findByIdAndUpdate(req.params.id, { author: req.body.authorId }, { new: true }).populate('author');
    if (book) {
      res.json(book);
    } else {
      res.status(404).send('Book not found');
    }
  } catch (error) {
    res.status(400).send(error.message);
  }
});

// Add a course to a student
app.put('/students/:id/courses', async (req, res) => {
  try {
    const student = await Student.findByIdAndUpdate(req.params.id, { $addToSet: { courses: req.body.courseId } }, { new: true }).populate('courses');
    if (student) {
      res.json(student);
    } else {
      res.status(404).send('Student not found');
    }
  } catch (error) {
    res.status(400).send(error.message);
  }
});

// Add a student to a course
app.put('/courses/:id/students', async (req, res) => {
  try {
    const course = await Course.findByIdAndUpdate(req.params.id, { $addToSet: { students: req.body.studentId } }, { new: true }).populate('students');
    if (course) {
      res.json(course);
    } else {
      res.status(404).send('Course not found');
    }
  } catch (error) {
    res.status(400).send(error.message);
  }
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **One-to-Many**: Link a book to an author using `book.author`.
2. **Many-to-Many**: Link students and courses using `student.courses` and `course.students`.

This setup demonstrates handling one-to-many and many-to-many relationships using Mongoose in an Express application.



## Error Handling with Databases

### 1. Managing Connection Errors

Handle MongoDB connection errors in Express.js using Mongoose.

**Demo Code:**

```typescript
import express from 'express';
import mongoose from 'mongoose';
import bodyParser from 'body-parser';

const app = express();
const port = 3000;

app.use(bodyParser.json());

// MongoDB connection
mongoose.connect('mongodb://localhost:27017/mydatabase', { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('Connected to MongoDB'))
  .catch(error => {
    console.error('Error connecting to MongoDB:', error.message);
    process.exit(1); // Exit process if connection fails
  });

const db = mongoose.connection;

// Monitor connection events
db.on('error', (error) => {
  console.error('MongoDB connection error:', error.message);
});

// Define a schema and model (example schema)
const exampleSchema = new mongoose.Schema({
  name: { type: String, required: true }
});

const Example = mongoose.model('Example', exampleSchema);

// Example route
app.get('/', async (req, res) => {
  try {
    const examples = await Example.find();
    res.json(examples);
  } catch (error) {
    console.error('Error retrieving data:', error.message);
    res.status(500).send('Internal Server Error');
  }
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Connection Errors**: Handle connection errors with `.catch()` during `mongoose.connect()`.
2. **Runtime Errors**: Monitor `db.on('error')` for runtime connection issues.

This setup manages MongoDB connection errors and provides robust error handling in an Express application.

### 2. Handling Validation Errors

Handle MongoDB validation errors in Express.js using Mongoose.

**Demo Code:**

```typescript
import express from 'express';
import mongoose from 'mongoose';
import bodyParser from 'body-parser';

const app = express();
const port = 3000;

app.use(bodyParser.json());

// MongoDB connection
mongoose.connect('mongodb://localhost:27017/mydatabase', { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('Connected to MongoDB'))
  .catch(error => {
    console.error('Error connecting to MongoDB:', error.message);
    process.exit(1);
  });

const db = mongoose.connection;
db.on('error', (error) => {
  console.error('MongoDB connection error:', error.message);
});

// Define a schema with validation
const exampleSchema = new mongoose.Schema({
  name: { type: String, required: true, minlength: 3 }
});

const Example = mongoose.model('Example', exampleSchema);

// Create a new record with validation
app.post('/examples', async (req, res) => {
  try {
    const newExample = new Example(req.body);
    const savedExample = await newExample.save();
    res.status(201).json(savedExample);
  } catch (error) {
    if (error.name === 'ValidationError') {
      res.status(400).json({ message: 'Validation error', errors: error.errors });
    } else {
      res.status(500).send('Internal Server Error');
    }
  }
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

**Usage:**

1. **Validation Errors**: Handle validation errors in the `catch` block of the save operation.
2. **Error Response**: Check for `error.name === 'ValidationError'` and respond with appropriate messages.

This setup demonstrates handling MongoDB validation errors using Mongoose in an Express application.

