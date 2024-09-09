# Deployment

## Environment Configuration
### 1. Using dotenv for Environment Variables

**Manage environment variables** using `dotenv` for configuration in Express apps.

**Example: Using `dotenv`**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const dotenv = require('dotenv');
   const app = express();

   // Load environment variables from .env file
   dotenv.config();

   // Access environment variables
   const PORT = process.env.PORT || 3000;

   app.get('/', (req, res) => {
     res.send('Environment configuration with dotenv!');
   });

   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **Create `.env` File**:

   ```
   // .env
   PORT=3000
   ```

3. **Install `dotenv`**:

   ```bash
   npm install dotenv
   ```

- **Use `dotenv.config()`** to load variables.
- **Store configuration** in a `.env` file.
- **Access variables** via `process.env`.

### 2. Managing Configurations Across Environments

**Handle different configurations** for various environments using `dotenv` and environment-specific files.

**Example: Environment Configuration**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const dotenv = require('dotenv');
   const path = require('path');
   const app = express();

   // Load environment-specific configuration
   const env = process.env.NODE_ENV || 'development';
   dotenv.config({ path: path.resolve(__dirname, `.env.${env}`) });

   // Access environment variables
   const PORT = process.env.PORT || 3000;

   app.get('/', (req, res) => {
     res.send(`Running in ${env} mode`);
   });

   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **Create Environment Files**:

   - **.env.development**:

     ```
     PORT=3000
     ```

   - **.env.production**:

     ```
     PORT=80
     ```

3. **Install `dotenv`**:

   ```bash
   npm install dotenv
   ```

- **Use `.env.<env>`** files for different environments.
- **Load configurations** based on `NODE_ENV`.
- **Access variables** via `process.env`.



## Deploying to Cloud Platforms
### 1. Deploying on AWS, Azure, Heroku

**Deploy Express apps** on AWS, Azure, or Heroku for cloud hosting.

**Example: Deploying on Heroku**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();
   const PORT = process.env.PORT || 3000;

   app.get('/', (req, res) => {
     res.send('Hello from Heroku!');
   });

   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **Create `Procfile`**:

   ```
   web: node server.js
   ```

3. **Deploy to Heroku**:

   ```bash
   # Initialize Git repository
   git init
   git add .
   git commit -m "Initial commit"
   
   # Login to Heroku
   heroku login
   
   # Create and deploy to Heroku
   heroku create
   git push heroku master
   
   # Open app in browser
   heroku open
   ```

- **Use `Procfile`** to define the web process.
- **Deploy via Git** using Heroku CLI.
- **Environment variables** are automatically managed.

### 2. Using Docker for Containerized Deployments

**Containerize and deploy Express apps** using Docker for consistent environments.

**Example: Dockerizing Express App**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();
   const PORT = process.env.PORT || 3000;

   app.get('/', (req, res) => {
     res.send('Hello from Docker!');
   });

   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **Create `Dockerfile`**:

   ```Dockerfile
   # Use official Node.js image
   FROM node:14

   # Set working directory
   WORKDIR /app

   # Copy package.json and install dependencies
   COPY package*.json ./
   RUN npm install

   # Copy application code
   COPY . .

   # Expose port and start application
   EXPOSE 3000
   CMD ["node", "server.js"]
   ```

3. **Build and Run Docker Container**:

   ```bash
   # Build Docker image
   docker build -t express-app .
   
   # Run Docker container
   docker run -p 3000:3000 express-app
   ```

- **Use `Dockerfile`** to define build steps.
- **Build and run** container with Docker commands.
- **Ensure consistent deployment** across environments.



## Monitoring and Logging in Production
### 1. Using PM2 for Process Management

**Manage and monitor Express apps** in production using PM2 for process management.

**Example: Using PM2**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const app = express();
   const PORT = process.env.PORT || 3000;

   app.get('/', (req, res) => {
     res.send('Running with PM2!');
   });

   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **Install and Start PM2**:

   ```bash
   # Install PM2 globally
   npm install -g pm2

   # Start the app with PM2
   pm2 start server.js --name express-app

   # Monitor processes
   pm2 monit

   # View logs
   pm2 logs
   ```

3. **Manage PM2 Process**:

   ```bash
   # List all processes
   pm2 list
   
   # Restart the app
   pm2 restart express-app
   
   # Stop the app
   pm2 stop express-app
   
   # Delete the app from PM2
   pm2 delete express-app
   ```

- **Use PM2** for process management and monitoring.
- **Access logs** and process metrics via PM2 commands.
- **Ensure app resilience** with automatic restarts and monitoring.

### 2. Log Management with Winston or Morgan

**Implement logging** in Express apps using Winston or Morgan for monitoring and debugging.

**Example: Using Winston**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const winston = require('winston');
   const app = express();
   const PORT = process.env.PORT || 3000;

   // Configure Winston
   const logger = winston.createLogger({
     level: 'info',
     format: winston.format.combine(
       winston.format.timestamp(),
       winston.format.json()
     ),
     transports: [
       new winston.transports.Console(),
       new winston.transports.File({ filename: 'app.log' })
     ]
   });

   app.use((req, res, next) => {
     logger.info(`${req.method} ${req.url}`);
     next();
   });

   app.get('/', (req, res) => {
     res.send('Logging with Winston!');
   });

   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **Install Winston**:

   ```bash
   npm install winston
   ```

**Example: Using Morgan**

1. **Setup Express App**:

   ```javascript
   // server.js
   const express = require('express');
   const morgan = require('morgan');
   const app = express();
   const PORT = process.env.PORT || 3000;

   // Use Morgan for HTTP request logging
   app.use(morgan('combined'));

   app.get('/', (req, res) => {
     res.send('Logging with Morgan!');
   });

   app.listen(PORT, () => {
     console.log(`Server running on port ${PORT}`);
   });
   ```

2. **Install Morgan**:

   ```bash
   npm install morgan
   ```

- **Use Winston** for custom logging and file storage.
- **Use Morgan** for HTTP request logging.
- **Choose based on** logging needs and complexity.