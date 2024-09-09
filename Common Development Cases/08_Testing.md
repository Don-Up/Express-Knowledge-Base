# Testing

## Unit Testing Routes and Middleware
### 1. Using Jest and Supertest

**Jest** and **Supertest** are used to test Express routes and middleware. Jest handles test execution, while Supertest makes HTTP requests.

**Example: Testing Routes with Jest and Supertest**

1. **Install Jest and Supertest**:

   ```bash
   npm install jest supertest --save-dev
   ```

2. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();

   app.get('/hello', (req, res) => {
     res.status(200).json({ message: 'Hello, World!' });
   });

   module.exports = app;
   ```

3. **Write Tests**:

   ```javascript
   // server.test.js
   const request = require('supertest');
   const app = require('./server'); // Import the app
   const { expect, test } = require('@jest/globals');

   test('GET /hello responds with JSON', async () => {
     const response = await request(app).get('/hello');
     expect(response.statusCode).toBe(200);
     expect(response.body).toEqual({ message: 'Hello, World!' });
   });
   ```

4. **Run Tests**:

   ```bash
   npx jest
   ```

- **Jest**: Runs and manages tests.
- **Supertest**: Simulates HTTP requests to test routes and middleware.

### 2. Mocking Dependencies

**Mocking dependencies** isolates and tests Express routes or middleware by replacing real dependencies with mocks.

**Example: Unit Testing with Mocks**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();

   app.use(express.json());

   app.post('/message', (req, res) => {
     const { sendMessage } = req.app.locals; // Dependency
     sendMessage(req.body.message);
     res.status(200).send('Message sent');
   });

   module.exports = app;
   ```

2. **Write Unit Tests with Mocks**:

   ```javascript
   // server.test.js
   const request = require('supertest');
   const app = require('./server');

   test('POST /message uses sendMessage dependency', async () => {
     const sendMessage = jest.fn(); // Mock dependency
     app.locals.sendMessage = sendMessage;

     const response = await request(app)
       .post('/message')
       .send({ message: 'Hello' });

     expect(response.statusCode).toBe(200);
     expect(response.text).toBe('Message sent');
     expect(sendMessage).toHaveBeenCalledWith('Hello');
   });
   ```

3. **Run Tests**:

   ```bash
   npx jest
   ```

- **Mocking Dependencies**: Allows isolated unit testing of routes and middleware, ensuring correct behavior without relying on real implementations.



## Integration Testing
### 1. Testing Full Routes with Database

Integration testing verifies the complete functionality of routes, including database interactions.

**Example: Integration Testing with Jest, Supertest, and a Mock Database**

1. **Setup Test Environment**:

   ```bash
   npm install jest supertest mongodb-memory-server mongoose --save-dev
   ```

2. **Setup Express App and Mongoose**:

   ```javascript
   // server.js
   const express = require('express');
   const mongoose = require('mongoose');
   const app = express();

   app.use(express.json());

   const userSchema = new mongoose.Schema({ name: String });
   const User = mongoose.model('User', userSchema);

   app.post('/users', async (req, res) => {
     const user = new User(req.body);
     await user.save();
     res.status(201).json(user);
   });

   module.exports = app;
   ```

3. **Write Integration Tests**:

   ```javascript
   // server.test.js
   const request = require('supertest');
   const mongoose = require('mongoose');
   const { MongoMemoryServer } = require('mongodb-memory-server');
   const app = require('./server');

   let mongoServer;

   beforeAll(async () => {
     mongoServer = await MongoMemoryServer.create();
     const uri = mongoServer.getUri();
     await mongoose.connect(uri, { useNewUrlParser: true, useUnifiedTopology: true });
   });

   afterAll(async () => {
     await mongoose.disconnect();
     await mongoServer.stop();
   });

   test('POST /users creates a new user', async () => {
     const response = await request(app)
       .post('/users')
       .send({ name: 'John Doe' });
     expect(response.statusCode).toBe(201);
     expect(response.body.name).toBe('John Doe');
   });
   ```

4. **Run Tests**:

   ```bash
   npx jest
   ```

- **Integration Testing**: Ensures that routes work correctly with the database, validating end-to-end functionality.

### 2. Validating Responses and Status Codes

**Integration testing** checks if routes return correct responses and status codes. Use **Jest** and **Supertest** for this.

**Example: Integration Testing Responses and Status Codes**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();

   app.use(express.json());

   app.get('/status', (req, res) => {
     res.status(200).json({ status: 'OK' });
   });

   app.post('/echo', (req, res) => {
     res.status(201).json(req.body);
   });

   module.exports = app;
   ```

2. **Write Tests**:

   ```javascript
   // server.test.js
   const request = require('supertest');
   const app = require('./server');
   const { expect, test } = require('@jest/globals');

   test('GET /status returns status 200 and JSON', async () => {
     const response = await request(app).get('/status');
     expect(response.statusCode).toBe(200);
     expect(response.body).toEqual({ status: 'OK' });
   });

   test('POST /echo returns posted data with status 201', async () => {
     const response = await request(app)
       .post('/echo')
       .send({ message: 'Hello' });
     expect(response.statusCode).toBe(201);
     expect(response.body).toEqual({ message: 'Hello' });
   });
   ```

3. **Run Tests**:

   ```bash
   npx jest
   ```

- **Integration Testing**: Ensures routes return expected responses and status codes, validating overall API functionality.



## End-to-End Testing
### 1. Using Cypress or Postman

**Cypress** and **Postman** facilitate end-to-end testing by simulating user interactions and validating full application workflows.

**Example: End-to-End Testing with Cypress**

1. **Install Cypress**:

   ```bash
   npm install cypress --save-dev
   ```

2. **Setup Cypress Test**:

   ```javascript
   // cypress/integration/app_spec.js
   describe('App End-to-End Tests', () => {
     it('should load the homepage and interact with form', () => {
       cy.visit('http://localhost:3000');
       cy.contains('Submit').click();
       cy.url().should('include', '/result');
       cy.get('form').find('input[name="name"]').type('John Doe');
       cy.get('form').submit();
       cy.contains('Form submitted successfully');
     });
   });
   ```

3. **Run Cypress**:

   ```bash
   npx cypress open
   ```

**Example: End-to-End Testing with Postman**

1. **Setup Postman Collection**:

   - Create a new collection and add requests for various endpoints.
   - Add tests in the Postman Collection Runner:

   ```javascript
   // Test script in Postman
   pm.test("Status code is 200", function () {
     pm.response.to.have.status(200);
   });

   pm.test("Response contains success message", function () {
     pm.response.to.have.jsonBody('message', 'Form submitted successfully');
   });
   ```

2. **Run Collection**:

   - Use the Postman Collection Runner to execute tests.

- **Cypress**: Tests full user interactions in a real browser.
- **Postman**: Validates API responses and status codes programmatically.

### 2. Automating Test Suites

Automate end-to-end test suites with **Cypress** or **Postman** using CI/CD pipelines.

**Example: Automating Cypress Tests**

1. **Setup Cypress**:

   ```bash
   npm install cypress --save-dev
   ```

2. **Add Cypress Script to `package.json`**:

   ```json
   "scripts": {
     "test:e2e": "cypress run"
   }
   ```

3. **Configure CI/CD Pipeline** (e.g., GitHub Actions):

   ```yaml
   # .github/workflows/e2e-tests.yml
   name: End-to-End Tests
   
   on: [push]
   
   jobs:
     e2e:
       runs-on: ubuntu-latest
       steps:
         - name: Checkout code
           uses: actions/checkout@v3
         - name: Install dependencies
           run: npm install
         - name: Run Cypress tests
           run: npm run test:e2e
   ```

**Example: Automating Postman Tests**

1. **Create a Collection in Postman**.

2. **Export Collection and Environment**.

3. **Add Postman Tests to CI/CD Pipeline**:

   ```yaml
   # .github/workflows/postman-tests.yml
   name: Postman Tests
   
   on: [push]
   
   jobs:
     test:
       runs-on: ubuntu-latest
       steps:
         - name: Checkout code
           uses: actions/checkout@v3
         - name: Install Newman
           run: npm install -g newman
         - name: Run Postman tests
           run: newman run path/to/collection.json -e path/to/environment.json
   ```

- **Cypress**: Automates browser-based tests.
- **Postman/Newman**: Runs API tests in CI/CD pipelines.