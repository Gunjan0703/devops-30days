# Dockerfile for Management-microservice 

## Nginx Proxy
```
FROM nginx:alpine

RUN addgroup -S nginx && adduser -S -G nginx nginx

COPY --chown=nginx:nginx sites-available/default.conf /etc/nginx/conf.d/default.conf

USER nginx

CMD ["nginx", "-g", "daemon off;"]
```
### **1. Minimal Base Image :**
* **Why important:** Alpine-based images are small (~5MB) and come with fewer packages → reduces attack surface.
* **Impact:** Faster image pulls and deployments.Smaller image size → less storage & bandwidth usage.More secure (fewer CVEs compared to full OS images).

### **2. Create Non-root User :**
* **Why important:** Creates a dedicated unprivileged user (nginx).
* **Impact:** Prevents container from running as root → better security.

### **3. Copy Config with Correct Ownership :**
* **Why important:** Ensures the Nginx config is owned by the non-root user.
* **Impact:** Avoids permission errors at runtime, config is version-controlled.

### **4. Run as Non-root User :**
* **Why important:** Drops root privileges when running the container.
* **Impact:** Limits damage if Nginx is compromised → aligns with security best practices.

### **5. Start Nginx in Foreground (PID 1) :**
* **Why important:** Runs Nginx in foreground, so it stays as the main process.
* **Impact:** Proper signal handling (graceful shutdown/restart) and better orchestration support.

## Frontend
```
FROM php:8.1.23-apache

RUN docker-php-ext-install mysqli pdo pdo_mysql opcache

WORKDIR /var/www/html

COPY --chown=www-data:www-data . .

USER www-data
```
### **1. Base Image :**
```
FROM php:8.1.23-apache
```
* **Why important:** Official PHP image with Apache pre-configured.
* **Impact:** Stable, secure, and reduces setup time since Apache + PHP are ready out of the box.

### **2. Install PHP Extensions :**
```
RUN docker-php-ext-install mysqli pdo pdo_mysql opcache
```
* **Why important:** Installs required PHP extensions (mysqli, pdo, pdo_mysql) for database connectivity and opcache for performance.
* **Impact:** Application has necessary database drivers, faster execution due to opcode caching.

### **3. Set Working Directory :**
```
WORKDIR /var/www/html
```
* **Why important:** Defines the default directory for all subsequent commands and application code.
* **Impact:** Cleaner paths in Dockerfile, easier file management, aligns with Apache default doc root.

### **4. Copy Source Code with Correct Ownership :**
```
COPY --chown=www-data:www-data . .
```
* **Why important:** Copies app code into the container and assigns ownership to www-data (Apache’s default user).
* **Impact:** Prevents file permission issues when Apache serves files.

### **5. Run as Non-root User :**
```
USER www-data
```
* **Why important:** Drops root privileges, running the container as Apache’s own non-root user.
* **Impact:** Improves security by following least-privilege principle → reduces risk if compromised.

## Backend
```
# =========================
# Stage 1: Build
# =========================
FROM node:18.20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production && npm cache clean --force
COPY . .

# =========================
# Stage 2: Production
# =========================
FROM node:18.20-alpine
RUN apk add --no-cache dumb-init
RUN addgroup -g 1001 -S nodejs \
    && adduser -S studentmgmt -u 1001 -G nodejs
WORKDIR /app
COPY --from=build /app /app
RUN chown -R studentmgmt:nodejs /app
USER studentmgmt
EXPOSE 3001
HEALTHCHECK --interval=30s --timeout=5s --start-period=30s --retries=3 \
  CMD node /app/healthcheck.js
ENTRYPOINT ["dumb-init", "--"]
CMD ["node", "server.js"]
```
### **1. Multi-stage build**

```dockerfile
FROM node:18.20-alpine AS build
...
FROM node:18.20-alpine
```
* **Why important:** Only the final compiled files (`dist`) go into the runtime image. Node and build tools don’t bloat the final image.
* **Impact:** Much smaller image and faster deployment.
  
### **2. Copy `package*.json` separately and run `npm ci`**

```dockerfile
COPY package*.json ./
RUN npm ci --only=production && npm cache clean --force 
```

* **Why important:** Docker caches this layer. If your code changes but dependencies don’t, Docker **reuses the cached dependencies**, saving time.
* **Impact:** Faster builds, efficient caching.

### **3. Copy Application Source :**
```
COPY . .
```
* **Why important:** Copies source code after dependencies, ensuring changes to code don’t invalidate the cached dependency layer.
* **Impact:** Efficient builds → reduces unnecessary reinstalling of dependencies.

### **4. Use dumb-init as Init System :**
```RUN apk add --no-cache dumb-init
ENTRYPOINT ["dumb-init", "--"]
```
* **Why important:** Handles PID 1 properly, forwards signals, and reaps zombie processes.
* **Impact:** Containers shut down gracefully, no orphan processes left running.

### **5. Create Non-root User :**
```RUN addgroup -g 1001 -S nodejs \
    && adduser -S studentmgmt -u 1001 -G nodejs
USER studentmgmt
```
* **Why important:** Drops root privileges → least-privilege principle.
* **Impact:** Improves container security, reduces risk if compromised.

### **6. Set Working Directory & Permissions :**
```WORKDIR /app
COPY --from=build /app /app
RUN chown -R studentmgmt:nodejs /app
```
* **Why important:** Keeps app files organized and assigns correct ownership.
* **Impact:** Prevents permission issues when non-root user runs Node.js.

### **7. Expose Application Port :**
```EXPOSE 3001
```
* **Why important:** Documents which port app listens on.
 **Impact:** Helpful for orchestration tools (Docker Compose, Kubernetes) to map ports correctly.

### **8. Healthcheck :**
```HEALTHCHECK --interval=30s --timeout=5s --start-period=30s --retries=3 \
  CMD node /app/healthcheck.js
```
* **Why important:** Docker monitors container health.
* **Impact:** Restart unhealthy containers automatically → better reliability.

### **9. Start App with CMD :**
```CMD ["node", "server.js"]
```
* **Why important:** Defines default startup command.
* **Impact:** Ensures container runs your app immediately.

## CONNECTING DATABASE WITH SCRIPT
* **db.js**
This is the database connection module.
Usually imports something like mysql2 / pg / mongoose.
Reads .env values using process.env.
Exports a connection or pool object.

# Dockerfile for Student Service

## Backend

```
FROM php:8.2-fpm
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    libzip-dev \
    libpq-dev \
    # The libmariadb-dev-compat package is often needed for pdo_mysql on debian
    libmariadb-dev-compat \
    && rm -rf /var/lib/apt/lists/*
RUN docker-php-ext-install pdo pdo_mysql zip
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer
WORKDIR /app
COPY . .
RUN composer install --no-interaction --prefer-dist --optimize-autoloader
EXPOSE 8000
CMD ["php-fpm"]
```
### **1. Use a More Appropriate Base Image :**
```FROM php:8.2-fpm
```
* **Why important:** php:fpm is designed to work with Nginx or Apache as a reverse proxy, making it a production-ready choice.
* **Impact:** Lightweight, stable, and optimized for PHP applications running with FPM.

### **2. Install Required System Dependencies :**
```RUN apt-get update && apt-get install -y \
    git \
    unzip \
    libzip-dev \
    libpq-dev \
    libmariadb-dev-compat \
    && rm -rf /var/lib/apt/lists/*
```
* **Why important:** Installs only essential tools (git, unzip) and libraries (libzip, libpq, mariadb) required for PHP extensions. Removes package lists afterwards to reduce image size.
* **Impact:** Ensures PHP can interact with MySQL/MariaDB, PostgreSQL, and handle zipping/unzipping while keeping image lean.

### **3. Install PHP Extensions :**
```RUN docker-php-ext-install pdo pdo_mysql zip
```
* **Why important:** Adds support for PDO (database abstraction), MySQL, and ZIP handling.
* **Impact:** Makes the container production-ready for typical PHP/Laravel/Symfony applications.

### **4. Copy Composer from Official Image :**
```COPY --from=composer:2 /usr/bin/composer /usr/bin/composer
```
* **Why important:** Reuses the trusted Composer binary from the official Composer image instead of downloading manually.
* **Impact:** Secure, consistent version of Composer without bloating the PHP image.

### **5. Set Working Directory :**
```WORKDIR /app
```
* **Why important:** Keeps application files organized inside the container.
* **Impact:** Cleaner file management, avoids path confusion.

### **6. Copy Application Files :**
```COPY . .
```
* **Why important:** Brings application code into the container.
* **Impact:** Ensures your PHP source and configs are available inside /app.

### **7. Install Composer Dependencies :**
```RUN composer install --no-interaction --prefer-dist --optimize-autoloader
```
* **Why important:** Installs project dependencies in production mode (--no-interaction, --prefer-dist, --optimize-autoloader).
* **Impact:** Faster builds, optimized autoloading → better runtime performance.

### **8. Start with php-fpm :**
```CMD ["php-fpm"]
```
* **Why important:** php-fpm is the default entrypoint for handling PHP requests with Nginx/Apache.
* **Impact:** Efficient request handling, better performance under load.

## Frontend 
```
FROM php:8.1-apache
WORKDIR /var/www/html
COPY . /var/www/html
RUN docker-php-ext-install mysqli pdo pdo_mysql
```
### **1. Use an Official Base Image with Apache :**
```FROM php:8.1-apache
```
* **Why important:** Combines PHP and Apache HTTP Server in one official image, making deployment straightforward.
* **Impact:** Faster setup, stable and secure since it’s maintained by the PHP team.

### **2. Set Working Directory :**
```WORKDIR /var/www/html
```
* **Why important:** Ensures all application files and operations happen in Apache’s document root.
* **Impact:** Keeps project structure consistent, avoids path-related issues.

### **3. Copy Application Files :**
```COPY . /var/www/html
```
* **Why important:** Copies the entire application source code into Apache’s default root.
* **Impact:** Makes PHP files directly available for serving by Apache.

### **4. Install PHP Extensions :**
```RUN docker-php-ext-install mysqli pdo pdo_mysql
```
* **Why important:** Enables MySQL database connectivity (mysqli, pdo_mysql) and PDO abstraction layer.
* **Impact:** Database-driven PHP applications (like WordPress, Laravel, or custom apps) can connect to MySQL/MariaDB out of the box.

# Docker-Compose file 
```
version: "3.8"

services:
  mysql:
    image: mysql:8
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: attendancemsystem
      MYSQL_USER: attendance_user
      MYSQL_PASSWORD: attendance_pass
    volumes:
      - ./infrastructure/mysql/attendancemsystem.sql:/docker-entrypoint-initdb.d/attendancemsystem.sql
      - mysql-data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-uattendance_user", "-pattendance_pass"]
      interval: 10s
      timeout: 5s
      retries: 5

  frontend:
    build: ./student-service/student-attendance-frontend
    container_name: student-frontend
    ports:
      - "8080:80"
    depends_on:
      - backend

  backend:
    build: ./student-service/student-attendance-backend
    container_name: backend-service
    restart: unless-stopped
    env_file:
      - ./student-service/student-attendance-backend/.env
    ports:
      - "8000:8000"
    depends_on:
      - mysql

  student-management-service:
    build: ./management-service/student-management-service
    container_name: student-management-service
    restart: always
    depends_on:
      mysql:
        condition: service_healthy
    environment:
      DB_HOST: mysql
      MYSQL_USER: attendance_user
      MYSQL_PASSWORD: attendance_pass
      MYSQL_NAME: attendancemsystem
    ports:
      - "3001:3001"

  # The container name has been changed to "management-frontend" to avoid conflict.
  management-frontend:
    build: ./management-service/student-frontend
    container_name: management-frontend
    restart: always
    depends_on:
      - student-management-service
      - mysql

  nginx:
    build: ./management-service/nginx
    container_name: nginx
    restart: always
    ports:
      - "80:80"
    depends_on:
      - management-frontend
      - student-management-service

volumes:
  mysql-data:
```

### **1. Environment Variable Management :***

- Uses .env files (env_file:) for sensitive configs like DB credentials.
  Prevents hardcoding secrets in the compose file.

### **2. Database Initialization :**

- Mounts SQL dump (attendancemsystem.sql) into docker-entrypoint-initdb.d/.
  Automates schema + seed setup on container startup.

### **3. Persistent Storage with Volumes :**

- mysql-data:/var/lib/mysql ensures DB data survives container restarts.
  Avoids data loss when containers are recreated.

### **4. Healthchecks :**

- mysql has a healthcheck with retries and intervals.
  Improves reliability by making sure dependent services wait until DB is ready.

Establishes correct startup order in multi-service systems.

### **5. Restart Policies :**

- restart: unless-stopped ensures backend services auto-restart on crashes or host reboots.
  Improves resilience in production.

### **6. Port Mapping :**

- Explicit ports (3306:3306, 8080:80, 8000:80, 3000:3000) make services accessible to host/dev environment.
  Maintains clarity on exposed entrypoints.
