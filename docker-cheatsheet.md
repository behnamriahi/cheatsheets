# 🐳 Docker Cheatsheet

## 📦 Images

``` bash
docker pull nginx               # دانلود ایمیج از رجیستری
docker images                   # لیست ایمیج‌های لوکال
docker rmi nginx                # حذف ایمیج از سیستم
docker build -t myapp .         # ساخت ایمیج از Dockerfile فعلی
docker tag myapp username/myapp:v1 # تگ‌گذاری برای انتشار
docker push username/myapp:v1   # ارسال ایمیج به رجیستری
```

------------------------------------------------------------------------

## ▶ Containers

``` bash
docker run nginx                         # اجرای کانتینر از ایمیج
docker run -d nginx                      # اجرا در پس‌زمینه (detached)
docker run -p 8080:80 nginx              # مپ‌کردن پورت‌ها
docker run -d --name mynginx -p 8080:80 nginx # نام‌گذاری کانتینر
docker ps                                # کانتینرهای درحال اجرا
docker ps -a                             # همه کانتینرها
docker stop mynginx                      # توقف کانتینر
docker start mynginx                     # شروع مجدد کانتینر
docker rm mynginx                        # حذف کانتینر
docker logs mynginx                      # مشاهده لاگ‌ها
docker stats                             # مصرف منابع کانتینرها
```

### 🔐 Exec into container

``` bash
docker exec -it mynginx bash  # ورود به کانتینر با bash
docker exec -it mynginx sh    # ورود به کانتینر با sh (سبک‌تر)
```

------------------------------------------------------------------------

## 📁 Volumes

``` bash
docker volume create myvolume     # ساخت ولوم
docker volume ls                  # لیست ولوم‌ها
docker run -v myvolume:/app/data nginx # اتصال ولوم به مسیر داخل کانتینر
docker run -v $(pwd):/app nginx   # مونت فولدر فعلی به کانتینر
```

------------------------------------------------------------------------

## 🌐 Networks

``` bash
docker network ls                 # لیست شبکه‌ها
docker network create mynetwork   # ساخت شبکه جدید
docker run --network mynetwork nginx # اتصال کانتینر به شبکه
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
docker build -t myapp .      # ساخت ایمیج
docker run -p 3000:3000 myapp # اجرای اپلیکیشن
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
docker compose up       # بالا آوردن سرویس‌ها
docker compose up -d    # اجرا در پس‌زمینه
docker compose down     # خاموش کردن و پاکسازی منابع
```

------------------------------------------------------------------------

## 🧹 Cleanup

``` bash
docker system prune     # پاکسازی منابع بلااستفاده
```

------------------------------------------------------------------------

# 🚀 Pro Tips

-   Use `.dockerignore`
-   Prefer multi-stage builds
-   Use environment variables
-   Keep images small
-   Use volumes for persistent data

------------------------------------------------------------------------

# 🧱 Multi-stage Build (پیشرفته)

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

- لایه‌بندی هوشمند: ابتدا فایل‌های وابستگی را کپی کن تا کش بهتر استفاده شود.
- از `npm ci` برای بیلد قابل تکرار استفاده کن.
- ایمیج‌های سبک مثل `alpine` برای runtime.
- حذف فایل‌های غیرضروری با `.dockerignore`.

نمونه `.dockerignore`:

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
docker run --env-file .env -p 3000:3000 myapp   # استفاده از فایل env
```

در Compose:

``` yaml
services:
  app:
    env_file:
      - .env
```

------------------------------------------------------------------------

# 🏭 Production Best Practices

- استفاده از `USER` غیر روت در Dockerfile.
- تنظیم `NODE_ENV=production` یا معادل آن.
- فعال‌سازی `HEALTHCHECK` برای مانیتورینگ.
- لاگ‌ها را به stdout/stderr بفرست.
- از secrets به جای env برای اطلاعات حساس استفاده کن.
- سیاست ری‌استارت مناسب (`--restart=always` یا `on-failure`).

نمونه کاربر غیر روت:

``` dockerfile
RUN addgroup -S app && adduser -S app -G app
USER app
```

------------------------------------------------------------------------

# 🧪 Debugging & Troubleshooting

``` bash
docker logs -f mynginx          # دنبال کردن لاگ‌ها
docker inspect mynginx          # مشاهده تنظیمات و متادیتا
docker top mynginx              # پردازش‌های داخل کانتینر
docker exec -it mynginx sh      # ورود برای دیباگ
docker cp mynginx:/app/logs ./  # کپی فایل از کانتینر
```

------------------------------------------------------------------------

# 🔁 CI/CD Integration (نمونه مفهومی)

مراحل پیشنهادی:
1. تست‌ها را اجرا کن
2. ایمیج بساز
3. ایمیج را تگ بزن (با نسخه/commit)
4. ایمیج را push کن
5. دیپلوی به محیط مقصد

نمونه دستورات CI:

``` bash
docker build -t registry.example.com/myapp:${GIT_SHA} .
docker push registry.example.com/myapp:${GIT_SHA}
```

------------------------------------------------------------------------

# 🏷 ساخت Registry خصوصی

``` bash
docker run -d -p 5000:5000 --name registry registry:2   # راه‌اندازی رجیستری
docker tag myapp localhost:5000/myapp:v1                # تگ برای رجیستری لوکال
docker push localhost:5000/myapp:v1                     # ارسال ایمیج
```

------------------------------------------------------------------------

# ☸️ استفاده با Kubernetes (نمونه ساده)

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
