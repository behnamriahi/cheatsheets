# CI/CD Cheatsheet

## مفاهیم اصلی

### مراحل پایپلاین CI/CD
```
1. Source        # مخزن کد (Git)
2. Build         # کامپایل/بسته‌بندی کد
3. Test          # اجرای تست‌های خودکار
4. Deploy        # استقرار در محیط
5. Monitor       # ردیابی عملکرد/خطاها
```

### بهترین شیوه‌ها
- Commit مکرر
- یکبار بساز، چندبار مستقر کن
- خودکارسازی تست در تمام سطوح
- سرعت پایپلاین را حفظ کن
- بیلدها تکرارپذیر باشند
- خطاها را زودتر کشف کن
- در محیط‌های شبیه به پروداکشن مستقر کن
- همه چیز را مانیتور کن

---

## GitHub Actions

### گردش کار پایه (.github/workflows/ci.yml)
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

### چندین Job
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

### استراتژی ماتریس
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

### Secretها و متغیرهای محیطی
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

### Artifactها
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

### کش کردن
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

### پایپلاین پایه (.gitlab-ci.yml)
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

### چندین Job در یک Stage
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

### محیط‌ها (Environments)
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

### بیلد Docker
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

### کش
```yaml
cache:
  paths:
    - node_modules/
  key: $CI_COMMIT_REF_SLUG
```

---

## Jenkins

### پایپلاین اعلانی (Jenkinsfile)
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

### Stageهای موازی
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

### Agent داکر
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

### پارامترها
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

## Docker در CI/CD

### Build و Push
```bash
# بیلد
docker build -t myapp:${VERSION} .

# تگ کردن
docker tag myapp:${VERSION} registry.example.com/myapp:${VERSION}
docker tag myapp:${VERSION} registry.example.com/myapp:latest

# Push کردن
docker push registry.example.com/myapp:${VERSION}
docker push registry.example.com/myapp:latest
```

### Dockerfile چند مرحله‌ای
```dockerfile
# مرحله بیلد
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# مرحله پروداکشن
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

---

## دستورات رایج

### Node.js/npm
```bash
npm ci                  # نصب تمیز (برای CI)
npm test               # اجرای تست‌ها
npm run build          # بیلد پروژه
npm run lint           # اجرای linter
npm audit              # بررسی امنیتی
```

### Python
```bash
pip install -r requirements.txt  # نصب وابستگی‌ها
pytest                           # اجرای تست‌ها
pylint .                         # Linting
python setup.py sdist            # بیلد توزیع
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

## مدیریت محیط

### توسعه (Development)
```bash
NODE_ENV=development
DEBUG=true
```

### Staging
```bash
NODE_ENV=staging
DATABASE_URL=staging-db.example.com
```

### پروداکشن (Production)
```bash
NODE_ENV=production
DATABASE_URL=prod-db.example.com
LOG_LEVEL=error
```

---

## استراتژی‌های استقرار

### استقرار Blue-Green
```
1. استقرار نسخه جدید (green) در کنار قدیمی (blue)
2. تست محیط green
3. تغییر ترافیک به green
4. نگهداری blue به عنوان گزینه بازگشت
```

### استقرار Canary
```
1. استقرار نسخه جدید در زیرمجموعه کوچکی از سرورها
2. مانیتور کردن معیارها
3. افزایش تدریجی ترافیک به نسخه جدید
4. بازگشت در صورت تشخیص مشکل
```

### استقرار Rolling
```
1. بروزرسانی سرورها یکی یکی
2. بررسی سلامت بعد از هر بروزرسانی
3. ادامه در صورت سلامت، بازگشت در غیر این صورت
```

---

## مانیتورینگ و اعلان‌ها

### بررسی سلامت
```bash
# نقطه پایانی HTTP
curl -f http://localhost:3000/health || exit 1

# اتصال دیتابیس
pg_isready -h localhost -p 5432

# وضعیت سرویس
systemctl is-active myservice
```

### اعلان‌ها
- وب‌هوک‌های Slack
- هشدارهای ایمیل
- PagerDuty
- Datadog
- Sentry
