# 🐳 Docker Cheatsheet

## 📦 Images

``` bash
docker pull nginx               # Download image from registry
docker images                   # List local images
docker rmi nginx                # Remove image from system
docker build -t myapp .         # Build image from current Dockerfile
docker tag myapp username/myapp:v1 # Tag for publishing
docker push username/myapp:v1   # Push image to registry
```

------------------------------------------------------------------------

## ▶ Containers

``` bash
docker run nginx                         # Run container from image
docker run -d nginx                      # Run in background (detached)
docker run -p 8080:80 nginx              # Map ports
docker run -d --name mynginx -p 8080:80 nginx # Name container
docker ps                                # Running containers
docker ps -a                             # All containers
docker stop mynginx                      # Stop container
docker start mynginx                     # Restart container
docker rm mynginx                        # Remove container
docker logs mynginx                      # View logs
docker stats                             # Container resource usage
```

### 🔐 Exec into container

``` bash
docker exec -it mynginx bash  # Enter container with bash
docker exec -it mynginx sh    # Enter container with sh (lighter)
```

------------------------------------------------------------------------

## 📁 Volumes

``` bash
docker volume create myvolume     # Create volume
docker volume ls                  # List volumes
docker run -v myvolume:/app/data nginx # Attach volume to path inside container
docker run -v $(pwd):/app nginx   # Mount current folder to container
```

------------------------------------------------------------------------

## 🌐 Networks

``` bash
docker network ls                 # List networks
docker network create mynetwork   # Create new network
docker run --network mynetwork nginx # Connect container to network
```

------------------------------------------------------------------------

## 🏗 Dockerfile Example (Node)

``` dockerfile
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

Build & Run:

``` bash
docker build -t myapp .      # Build image
docker run -p 3000:3000 myapp # Run application
```

------------------------------------------------------------------------

## 🧩 Docker Compose

### docker-compose.yml

``` yaml
version: "3.9"
services:
  app:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - db
  db:
    image: postgres
    environment:
      POSTGRES_PASSWORD: example
```

### Commands

``` bash
docker compose up       # Start services
docker compose up -d    # Run in background
docker compose down     # Stop and cleanup resources
```

------------------------------------------------------------------------

## 🧹 Cleanup

``` bash
docker system prune     # Clean unused resources
```

------------------------------------------------------------------------

# 🚀 Pro Tips

-   Use `.dockerignore`
-   Prefer multi-stage builds
-   Use environment variables
-   Keep images small
-   Use volumes for persistent data

------------------------------------------------------------------------

# 🧱 Multi-stage Build (Advanced)

``` dockerfile
# Stage 1: build
FROM node:18 AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 2: runtime
FROM node:18-alpine
WORKDIR /app
ENV NODE_ENV=production
COPY --from=build /app/dist ./dist
COPY --from=build /app/package*.json ./
RUN npm ci --only=production
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

------------------------------------------------------------------------

# ⚙️ Dockerfile Optimization

- Smart layering: Copy dependency files first for better caching.
- Use `npm ci` for reproducible builds.
- Light images like `alpine` for runtime.
- Remove unnecessary files with `.dockerignore`.

Example `.dockerignore`:

```
node_modules
dist
.git
.env
*.log
```

------------------------------------------------------------------------

# 🩺 Healthcheck

``` dockerfile
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1
```

------------------------------------------------------------------------

# 🔐 Env Files

``` bash
docker run --env-file .env -p 3000:3000 myapp   # Use env file
```

In Compose:

``` yaml
services:
  app:
    env_file:
      - .env
```

------------------------------------------------------------------------

# 🏭 Production Best Practices

- Use non-root `USER` in Dockerfile.
- Set `NODE_ENV=production` or equivalent.
- Enable `HEALTHCHECK` for monitoring.
- Send logs to stdout/stderr.
- Use secrets instead of env for sensitive data.
- Proper restart policy (`--restart=always` or `on-failure`).

Non-root user example:

``` dockerfile
RUN addgroup -S app && adduser -S app -G app
USER app
```

------------------------------------------------------------------------

# 🧪 Debugging & Troubleshooting

``` bash
docker logs -f mynginx          # Follow logs
docker inspect mynginx          # View configuration and metadata
docker top mynginx              # Processes inside container
docker exec -it mynginx sh      # Enter for debugging
docker cp mynginx:/app/logs ./  # Copy file from container
```

------------------------------------------------------------------------

# 🔁 CI/CD Integration (Conceptual Example)

Recommended steps:
1. Run tests
2. Build image
3. Tag image (with version/commit)
4. Push image
5. Deploy to target environment

Example CI commands:

``` bash
docker build -t registry.example.com/myapp:${GIT_SHA} .
docker push registry.example.com/myapp:${GIT_SHA}
```

------------------------------------------------------------------------

# 🏷 Private Registry Setup

``` bash
docker run -d -p 5000:5000 --name registry registry:2   # Start registry
docker tag myapp localhost:5000/myapp:v1                # Tag for local registry
docker push localhost:5000/myapp:v1                     # Push image
```

------------------------------------------------------------------------

# ☸️ Use with Kubernetes (Simple Example)

Deployment:

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: myapp
          image: registry.example.com/myapp:v1
          ports:
            - containerPort: 3000
          readinessProbe:
            httpGet:
              path: /health
              port: 3000
```

Service:

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
  ports:
    - port: 80
      targetPort: 3000
  type: ClusterIP
```
