````markdown
# CI/CD Cheatsheet

## Core Concepts

### CI/CD Pipeline Stages
```
1. Source        # Code repository (Git)
2. Build         # Compile/package code
3. Test          # Run automated tests
4. Deploy        # Deploy to environment
5. Monitor       # Track performance/errors
```

### Best Practices
- Commit frequently
- Build once, deploy many
- Automate testing at all levels
- Keep pipeline fast
- Make builds reproducible
- Fail fast
- Deploy to production-like environments
- Monitor everything

---

## GitHub Actions

### Basic Workflow (.github/workflows/ci.yml)
```yaml
name: CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Run tests
      run: npm test
      
    - name: Build
      run: npm run build
```

### Multiple Jobs
```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm test
      
  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm run build
      
  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - run: echo "Deploying..."
```

### Matrix Strategy
```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]
        node: [14, 16, 18]
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node }}
      - run: npm test
```

### Secrets and Environment Variables
```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    env:
      NODE_ENV: production
    steps:
      - name: Deploy
        run: echo "Deploying to ${{ secrets.SERVER }}"
        env:
          API_KEY: ${{ secrets.API_KEY }}
```

### Artifacts
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm run build
      
      - name: Upload artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-files
          path: dist/
          
  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Download artifacts
        uses: actions/download-artifact@v3
        with:
          name: build-files
```

### Caching
```yaml
- name: Cache dependencies
  uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-
```

---

## GitLab CI/CD

### Basic Pipeline (.gitlab-ci.yml)
```yaml
stages:
  - build
  - test
  - deploy

variables:
  NODE_ENV: production

build:
  stage: build
  image: node:18
  script:
    - npm ci
    - npm run build
  artifacts:
    paths:
      - dist/

test:
  stage: test
  image: node:18
  script:
    - npm ci
    - npm test
  coverage: '/Coverage: \d+\.\d+%/'

deploy:
  stage: deploy
  script:
    - echo "Deploying application..."
  only:
    - main
```

### Multiple Jobs in One Stage
```yaml
test:unit:
  stage: test
  script:
    - npm run test:unit

test:integration:
  stage: test
  script:
    - npm run test:integration
```

### Environments
```yaml
deploy:staging:
  stage: deploy
  script:
    - deploy_to_staging.sh
  environment:
    name: staging
    url: https://staging.example.com
  only:
    - develop

deploy:production:
  stage: deploy
  script:
    - deploy_to_prod.sh
  environment:
    name: production
    url: https://example.com
  only:
    - main
  when: manual
```

### Docker Build
```yaml
build:docker:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t myapp:$CI_COMMIT_SHA .
    - docker push myapp:$CI_COMMIT_SHA
```

### Cache
```yaml
cache:
  paths:
    - node_modules/
  key: $CI_COMMIT_REF_SLUG
```

---

## Jenkins

### Declarative Pipeline (Jenkinsfile)
```groovy
pipeline {
    agent any
    
    environment {
        NODE_ENV = 'production'
    }
    
    stages {
        stage('Build') {
            steps {
                sh 'npm ci'
                sh 'npm run build'
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                sh './deploy.sh'
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
```

### Parallel Stages
```groovy
stage('Test') {
    parallel {
        stage('Unit Tests') {
            steps {
                sh 'npm run test:unit'
            }
        }
        stage('Integration Tests') {
            steps {
                sh 'npm run test:integration'
            }
        }
    }
}
```

### Docker Agent
```groovy
pipeline {
    agent {
        docker {
            image 'node:18'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm ci'
            }
        }
    }
}
```

### Parameters
```groovy
pipeline {
    parameters {
        string(name: 'BRANCH', defaultValue: 'main', description: 'Branch to build')
        choice(name: 'ENVIRONMENT', choices: ['dev', 'staging', 'prod'], description: 'Environment')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests?')
    }
    
    stages {
        stage('Build') {
            steps {
                echo "Building ${params.BRANCH} for ${params.ENVIRONMENT}"
            }
        }
    }
}
```

---

## CircleCI

### Basic Config (.circleci/config.yml)
```yaml
version: 2.1

jobs:
  build:
    docker:
      - image: cimg/node:18.0
    steps:
      - checkout
      - restore_cache:
          keys:
            - v1-deps-{{ checksum "package-lock.json" }}
      - run:
          name: Install dependencies
          command: npm ci
      - save_cache:
          paths:
            - node_modules
          key: v1-deps-{{ checksum "package-lock.json" }}
      - run:
          name: Build
          command: npm run build
      - persist_to_workspace:
          root: .
          paths:
            - dist

  test:
    docker:
      - image: cimg/node:18.0
    steps:
      - checkout
      - restore_cache:
          keys:
            - v1-deps-{{ checksum "package-lock.json" }}
      - run: npm test

  deploy:
    docker:
      - image: cimg/node:18.0
    steps:
      - attach_workspace:
          at: .
      - run: ./deploy.sh

workflows:
  build-test-deploy:
    jobs:
      - build
      - test:
          requires:
            - build
      - deploy:
          requires:
            - test
          filters:
            branches:
              only: main
```

---

## Travis CI

### Basic Config (.travis.yml)
```yaml
language: node_js
node_js:
  - '18'
  - '16'

cache:
  directories:
    - node_modules

install:
  - npm ci

script:
  - npm test
  - npm run build

deploy:
  provider: pages
  skip_cleanup: true
  github_token: $GITHUB_TOKEN
  local_dir: dist
  on:
    branch: main
```

---

## Azure Pipelines

### Basic Pipeline (azure-pipelines.yml)
```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

variables:
  NODE_VERSION: '18.x'

stages:
- stage: Build
  jobs:
  - job: BuildJob
    steps:
    - task: NodeTool@0
      inputs:
        versionSpec: $(NODE_VERSION)
    - script: npm ci
      displayName: 'Install dependencies'
    - script: npm run build
      displayName: 'Build'
    - publish: dist
      artifact: dist

- stage: Test
  dependsOn: Build
  jobs:
  - job: TestJob
    steps:
    - task: NodeTool@0
      inputs:
        versionSpec: $(NODE_VERSION)
    - script: npm test
      displayName: 'Run tests'

- stage: Deploy
  dependsOn: Test
  condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))
  jobs:
  - deployment: DeployJob
    environment: production
    strategy:
      runOnce:
        deploy:
          steps:
          - download: current
            artifact: dist
          - script: echo "Deploying..."
```

---

## Docker in CI/CD

### Build and Push
```bash
# Build
docker build -t myapp:${VERSION} .

# Tag
docker tag myapp:${VERSION} registry.example.com/myapp:${VERSION}
docker tag myapp:${VERSION} registry.example.com/myapp:latest

# Push
docker push registry.example.com/myapp:${VERSION}
docker push registry.example.com/myapp:latest
```

### Multi-stage Dockerfile
```dockerfile
# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

---

## Common Commands

### Node.js/npm
```bash
npm ci                  # Clean install (for CI)
npm test               # Run tests
npm run build          # Build project
npm run lint           # Run linter
npm audit              # Security check
```

### Python
```bash
pip install -r requirements.txt  # Install dependencies
pytest                           # Run tests
pylint .                         # Linting
python setup.py sdist            # Build distribution
```

### Docker
```bash
docker build -t app .
docker run app
docker push registry/app
docker-compose up -d
```

### Git
```bash
git tag v1.0.0
git push --tags
git describe --tags
```

---

## Environment Management

### Development
```bash
NODE_ENV=development
DEBUG=true
```

### Staging
```bash
NODE_ENV=staging
DATABASE_URL=staging-db.example.com
```

### Production
```bash
NODE_ENV=production
DATABASE_URL=prod-db.example.com
LOG_LEVEL=error
```

---

## Deployment Strategies

### Blue-Green Deployment
```
1. Deploy new version (green) alongside old (blue)
2. Test green environment
3. Switch traffic to green
4. Keep blue as rollback option
```

### Canary Deployment
```
1. Deploy new version to small subset of servers
2. Monitor metrics
3. Gradually increase traffic to new version
4. Rollback if issues detected
```

### Rolling Deployment
```
1. Update servers one by one
2. Check health after each update
3. Continue if healthy, rollback otherwise
```

---

## Monitoring and Alerts

### Health Checks
```bash
# HTTP endpoint
curl -f http://localhost:3000/health || exit 1

# Database connection
pg_isready -h localhost -p 5432

# Service status
systemctl is-active myservice
```

### Notifications
- Slack webhooks
- Email alerts
- PagerDuty
- Datadog
- Sentry

````
