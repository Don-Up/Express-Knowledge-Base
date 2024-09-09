# Configuration Management

## Centralizing Configuration
### 1. Using config Packages

**Centralize** configuration settings using packages like `config`.

**Example: Centralized Configuration with `config`**

1. **Setup Express App**:

   ```plaintext
   my-app/
   ├── config/
   │   ├── default.json
   │   └── production.json
   ├── server.js
   ```

2. **Files**:

   - **config/default.json**:

     ```json
     {
       "port": 3000,
       "db": {
         "host": "localhost",
         "port": 27017
       }
     }
     ```

   - **config/production.json**:

     ```json
     {
       "port": 80,
       "db": {
         "host": "prod-db-host",
         "port": 27017
       }
     }
     ```

   - **server.js**:

     ```javascript
     // server.js
     const express = require('express');
     const config = require('config'); // npm install config
     const app = express();
     
     // Load configuration
     const port = config.get('port');
     const dbConfig = config.get('db');
     
     console.log(`Connecting to database at ${dbConfig.host}:${dbConfig.port}`);
     
     app.listen(port, () => {
       console.log(`Server running on port ${port}`);
     });
     ```

- **Use `config`** to manage different environments (e.g., `default.json`, `production.json`).
- **Centralize settings** like ports and database configuration in JSON files.
- **Load and use** configuration values in your application code.

### 2. Environment-Specific Settings

**Manage** environment-specific settings using `dotenv` for loading environment variables.

**Example: Environment-Specific Configuration with `dotenv`**

1. **Setup Express App**:

   ```plaintext
   my-app/
   ├── .env
   ├── .env.production
   ├── server.js
   ```

2. **Files**:

   - **.env** (Development):

     ```plaintext
     PORT=3000
     DB_HOST=localhost
     DB_PORT=27017
     ```

   - **.env.production** (Production):

     ```plaintext
     PORT=80
     DB_HOST=prod-db-host
     DB_PORT=27017
     ```

   - **server.js**:

     ```javascript
     // server.js
     require('dotenv').config(); // npm install dotenv
     const express = require('express');
     const app = express();
     
     // Load environment variables
     const port = process.env.PORT || 3000;
     const dbHost = process.env.DB_HOST;
     const dbPort = process.env.DB_PORT;
     
     console.log(`Connecting to database at ${dbHost}:${dbPort}`);
     
     app.listen(port, () => {
       console.log(`Server running on port ${port}`);
     });
     ```

- **Use `.env`** files to define environment-specific variables.
- **Load variables** into your application using `dotenv`.
- **Switch between environments** by setting environment variables before starting the server.

### 3. DevOps Practices

**Implement** DevOps practices by managing configuration with environment variables and CI/CD pipelines.

**Example: Using Environment Variables and CI/CD**

1. **Setup Express App**:

   ```plaintext
   my-app/
   ├── server.js
   ├── .env (for local development)
   ├── config/
   │   └── config.js (for staging/production)
   ```

2. **Files**:

   - **.env** (Local Development):

     ```plaintext
     PORT=3000
     DB_HOST=localhost
     DB_PORT=27017
     ```

   - **config/config.js** (For CI/CD):

     ```javascript
     // config/config.js
     module.exports = {
       port: process.env.PORT || 3000,
       db: {
         host: process.env.DB_HOST || 'localhost',
         port: process.env.DB_PORT || 27017
       }
     };
     ```

   - **server.js**:

     ```javascript
     // server.js
     const express = require('express');
     const config = require('./config/config'); // Load configuration
     
     const app = express();
     const port = config.port;
     const dbHost = config.db.host;
     const dbPort = config.db.port;
     
     console.log(`Connecting to database at ${dbHost}:${dbPort}`);
     
     app.listen(port, () => {
       console.log(`Server running on port ${port}`);
     });
     ```

- **Use `.env`** for local development and environment variables for production.
- **Integrate configuration** with CI/CD pipelines by setting environment variables on deployment platforms (e.g., AWS, Azure).
- **Centralize configuration** management to ensure consistency across environments.



## CI/CD Pipelines for Express Apps
### 1. Automating Tests and Deployments

**Automate** testing and deployment with CI/CD pipelines using GitHub Actions.

**Example: GitHub Actions for Testing and Deployment**

1. **Setup Pipeline**:

   ```plaintext
   my-app/
   ├── .github/
   │   └── workflows/
   │       └── ci-cd.yml
   ├── server.js
   ```

2. **Files**:

   - **.github/workflows/ci-cd.yml**:

     ```yaml
     name: CI/CD Pipeline
     
     on:
       push:
         branches:
           - main
       pull_request:
         branches:
           - main
     
     jobs:
       build:
         runs-on: ubuntu-latest
     
         steps:
           - name: Checkout code
             uses: actions/checkout@v3
     
           - name: Set up Node.js
             uses: actions/setup-node@v3
             with:
               node-version: '14'
     
           - name: Install dependencies
             run: npm install
     
           - name: Run tests
             run: npm test
     
           - name: Deploy
             run: |
               echo "Deploying to production"
               # Add deployment script/commands here
     ```

- **Configure** GitHub Actions to run on pushes or pull requests.
- **Automate** `npm install` and `npm test` to ensure code quality.
- **Deploy** your app by adding deployment commands or scripts.

### 2. Using GitHub Actions, Jenkins

**Automate** builds and deployments with GitHub Actions and Jenkins.

**Example: GitHub Actions and Jenkins Configuration**

1. **GitHub Actions Setup**:

   ```yaml
   # .github/workflows/ci-cd.yml
   name: CI/CD Pipeline

   on:
     push:
       branches:
         - main

   jobs:
     build:
       runs-on: ubuntu-latest

       steps:
         - name: Checkout code
           uses: actions/checkout@v3

         - name: Set up Node.js
           uses: actions/setup-node@v3
           with:
             node-version: '14'

         - name: Install dependencies
           run: npm install

         - name: Run tests
           run: npm test

         - name: Deploy
           run: |
             echo "Deploying to production"
             # Deploy script
   ```

2. **Jenkins Setup**:

   - **Jenkinsfile**:

     ```groovy
     pipeline {
       agent any
       stages {
         stage('Checkout') {
           steps {
             git 'https://github.com/your-repo.git'
           }
         }
         stage('Install Dependencies') {
           steps {
             sh 'npm install'
           }
         }
         stage('Run Tests') {
           steps {
             sh 'npm test'
           }
         }
         stage('Deploy') {
           steps {
             echo 'Deploying to production'
             // Deploy script
           }
         }
       }
     }
     ```

- **GitHub Actions**: Automate testing and deployment for each push to `main`.
- **Jenkins**: Configure a Jenkins pipeline for continuous integration and deployment.
- **Scripts**: Add deployment commands in both setups to deploy your Express app.