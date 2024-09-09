# DevOps Practices

## CI/CD Pipelines for Express Apps
### 1. Automating Tests and Deployments

**Automate/ˈɔːtəmeɪt/** testing and deployment with CI/CD pipelines using GitHub Actions or Jenkins.

**Example: GitHub Actions for CI/CD**

1. **Setup Pipeline**:

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
             # Add deployment script here
   ```

- **Automate/ˈɔːtəmeɪt/** `npm install`, `npm test`, and deployment with GitHub Actions.
- **Run** tests and deploy automatically on code changes to the `main` branch.

### 2. Using GitHub Actions, Jenkins

**Implement** CI/CD pipelines with GitHub Actions and Jenkins for Express apps.

**Example: GitHub Actions Pipeline**

1. **GitHub Actions**:

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
         - uses: actions/checkout@v3
         - uses: actions/setup-node@v3
           with:
             node-version: '14'
         - run: npm install
         - run: npm test
         - run: |
             echo "Deploying to production"
             # Deploy script here
   ```

**Example: Jenkins Pipeline**

1. **Jenkinsfile**:

   ```groovy
   pipeline {
     agent any
     stages {
       stage('Checkout') {
         steps {
           git 'https://github.com/your-repo.git'
         }
       }
       stage('Install and Test') {
         steps {
           sh 'npm install'
           sh 'npm test'
         }
       }
       stage('Deploy') {
         steps {
           echo 'Deploying to production'
           // Deploy script here
         }
       }
     }
   }
   ```

- **GitHub Actions**: Automate/ˈɔːtəmeɪt/ builds, tests, and deployments on code changes.
- **Jenkins**: Configure pipelines for similar CI/CD tasks.
- **Deployment Scripts**: Add deployment logic as needed.