### یک سناریو سخت
---
- خوده سناریو
```
# Scenario: Advanced Docker Compose Practice

# پروژه شامل 5 سرویس است:
# nginx
# app
# db
# redis
# adminer

# --------------------------------------------------
# Service 1: nginx
# --------------------------------------------------

# service name:
#   nginx

# image:
#   nginx:1.27

# container_name:
#   nginx-proxy

# port mapping:
#   8080:80

# networks:
#   frontend-net

# restart policy:
#   unless-stopped

# depends_on:
#   app

# bind mount:
#   ./nginx.conf:/etc/nginx/nginx.conf:ro


# --------------------------------------------------
# Service 2: app
# --------------------------------------------------

# service name:
#   app

# image:
#   httpd:2.4

# container_name:
#   app

# networks:
#   frontend-net
#   backend-net

# environment:
#   APP_ENV=production
#   DB_HOST=db
#   REDIS_HOST=redis

# restart policy:
#   unless-stopped

# depends_on:
#   db
#   redis


# --------------------------------------------------
# Service 3: db
# --------------------------------------------------

# service name:
#   db

# image:
#   postgres:17

# container_name:
#   database

# environment:
#   POSTGRES_USER=arian
#   POSTGRES_PASSWORD=Dev123456
#   POSTGRES_DB=appdb

# volume:
#   db-data:/var/lib/postgresql/data

# network:
#   backend-net

# restart policy:
#   unless-stopped

# healthcheck:
#   use pg_isready


# --------------------------------------------------
# Service 4: redis
# --------------------------------------------------

# service name:
#   redis

# image:
#   redis:7

# container_name:
#   redis-cache

# network:
#   backend-net

# volume:
#   redis-data:/data

# restart policy:
#   unless-stopped


# --------------------------------------------------
# Service 5: adminer
# --------------------------------------------------

# service name:
#   adminer

# image:
#   adminer:latest

# container_name:
#   adminer

# port mapping:
#   8081:8080

# network:
#   backend-net

# depends_on:
#   db

# restart policy:
#   unless-stopped


# --------------------------------------------------
# Define resources
# --------------------------------------------------

# volumes:
#   db-data
#   redis-data

# networks:
#   frontend-net
#   backend-net


# --------------------------------------------------
# Tasks
# --------------------------------------------------

# 1. compose.yaml را کامل بنویس
# 2. فایل nginx.conf جداگانه بساز
# 3. nginx باید درخواست‌ها را به service app بفرستد
# 4. docker compose config اجرا کن
# 5. docker compose up -d
# 6. docker compose ps
# 7. docker compose logs
# 8. healthcheck دیتابیس را بررسی کن
# 9. networkها را inspect کن
# 10. volumeها را inspect کن
# 11. در مرورگر تست کن:
#     http://localhost:8080
#     http://localhost:8081
```

- کد هایه سناریو
```
services:
  nginx:
    image: nginx:1.27
    container_name: nginx-proxy
    ports:
      - "8080:80"
    networks:
      - frontend-net
    restart: unless-stopped
    depends_on:
      - app
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
        

  app:
    image: httpd:2.4
    container_name: app
    networks:
      - frontend-net
      - backend-net
    environment:
      APP_ENV: "production"
      DB_HOST: "db"
      REDIS_HOST: "redis"
    restart: unless-stopped
    depends_on:
      - db
      - redis

  db:
    image: postgres:17
    container_name: database
    environment:
      POSTGRES_USER: "arian"
      POSTGRES_PASSWORD: "Dev123456"
      POSTGRES_DB: "appdb"
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - backend-net
    restart: unless-stopped

  redis:
    image: redis:7
    container_name: redis-cache
    networks:
      - backend-net
    volumes:
      - redis-data:/data
    restart: unless-stopped


  adminer:
    image: adminer:latest
    container_name: adminer
    ports:
      - "8081:8080"
    networks:
      - backend-net
    depends_on:
      - db
    restart: unless-stopped


volumes:
  db-data:
  redis-data:

networks:
  frontend-net:
  backend-net:

```


<p align="center">
	<img src="../../../00-images/dockerveryhighlevelcompose.png" alt="" width=1000>
</p>