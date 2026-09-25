## دستور `docker inspect` چیست؟

```bash
docker inspect
```

برای **دیدن جزئیات کامل و اطلاعات داخلی یک Object در Docker** استفاده می‌شود.

ا-Objectهایی مثل:

- Container
    
- Image
    
- Network
    
- Volume
    

را می‌توانی Inspect کنی.

---

## ساختار دستور:

```bash
docker inspect OBJECT_NAME
```

مثال:

```bash
docker inspect mynginx
```

ا-Docker یک خروجی **JSON خیلی کامل** برمی‌گرداند.

---

## مثال واقعی برای Container

اول Container داریم:

```bash
docker run -d --name web nginx
```

حالا:

```bash
docker inspect web
```

خروجی شامل اطلاعاتی مثل:

```json
{
    "Id": "abc123",
    "Name": "/web",
    "Config": {
        "Image": "nginx"
    },
    "NetworkSettings": {
        "IPAddress": "172.17.0.2"
    }
}
```

---

## چه اطلاعاتی می‌دهد؟

### 1) اطلاعات کلی Container

مثل:

- ID
    
- Name
    
- Image استفاده شده
    
- زمان ساخته شدن
    

---

### 2) Network

مثلاً:

```json
"IPAddress": "172.17.0.2"
```

یعنی IP داخلی Container.

---

### 3) Port Mapping

مثلاً:

```json
"Ports": {
    "80/tcp": [
        {
          "HostPort": "8080"
        }
    ]
}
```

یعنی:

```text
Host Port 8080
       |
       ↓
Container Port 80
```

---

### 4) Volumeها

می‌بینی چه Storageهایی به Container وصل شده‌اند.

مثلاً:

```json
"Mounts": [
 {
   "Source": "/data",
   "Destination": "/app/data"
 }
]
```

---

### 5) Environment Variables

مثلاً:

```json
"Env": [
 "MYSQL_ROOT_PASSWORD=123"
]
```

---

# ا-Inspect برای Image

مثلاً:

```bash
docker inspect nginx
```

اطلاعاتی مثل:

- Layerها
    
- Architecture
    
- OS
    
- Command پیش‌فرض
    
- Environment
    

را نشان می‌دهد.

---

# یک کاربرد خیلی مهم: گرفتن فقط یک بخش خاص

چون خروجی inspect خیلی بزرگ است، معمولاً با `--format` استفاده می‌شود.

مثلاً گرفتن IP Container:

```bash
docker inspect -f '{{.NetworkSettings.IPAddress}}' web
```

خروجی:

```text
172.17.0.2
```

---

گرفتن Image یک Container:

```bash
docker inspect -f '{{.Config.Image}}' web
```

خروجی:

```text
nginx
```

---

## تفاوت `docker ps` و `docker inspect`

### `docker ps`

اطلاعات خلاصه:

```bash
docker ps
```

مثلاً:

```text
NAME    IMAGE    PORTS
web     nginx    8080->80
```

---

### `docker inspect`

اطلاعات کامل:

```bash
docker inspect web
```

مثلاً:

- IP
    
- Network
    
- Volume
    
- Environment
    
- Command
    
- Config
    

---

## ذهنیت DevOps:

وقتی مشکلی داری:

مثلاً:

"چرا Container به دیتابیس وصل نمی‌شود؟"

معمولاً می‌روی سراغ:

```bash
docker inspect container_name
```

و بررسی می‌کنی:

- IP درست است؟
    
- Network درست است؟
    
- Volume وصل است؟
    
- Environment درست است؟
    

---

خلاصه:

```text
docker ps
      ↓
نمایش سریع وضعیت

docker inspect
      ↓
کالبدشکافی کامل Object
```

ا-`docker inspect` یکی از مهم‌ترین ابزارهای Debug در Docker است.

--- 
# چرا یکی از ابزار هایه خفنه debug است به چه دلیل و چجوری میتونه  debug کنه؟

ا-`docker inspect` برای Debug کردن یعنی **داخل اطلاعات واقعی Container/Image بگردی تا بفهمی مشکل از کجاست**. این دستور خودش مشکل را حل نمی‌کند؛ اطلاعاتی می‌دهد که با آن علت خطا را پیدا می‌کنی.

چند سناریوی واقعی:

---

## 1) مشکل اتصال Containerها به هم (Network Debug)

فرض کن:

-ا-Backend داری
    
- ا-Database داری
    
- ا-Backend به Database وصل نمی‌شود.
    

اول Network را چک می‌کنی:

```bash
docker inspect backend
```

قسمت:

```json
"Networks": {
    "my-network": {
        "IPAddress": "172.18.0.3"
    }
}
```

بررسی می‌کنی:

- آیا داخل همان Network هستند؟
    
- IP دارند؟
    
- اسم Network درست است؟
    

مثلاً:

```bash
docker inspect mysql
```

می‌بینی:

```json
"Networks": {
    "my-network": {}
}
```

پس هر دو داخل یک Network هستند.

---

# 2) مشکل Port Mapping

مثلاً Nginx بالا است ولی از بیرون باز نمی‌شود.

می‌زنی:

```bash
docker inspect nginx-container
```

قسمت:

```json
"Ports": {
    "80/tcp": [
        {
            "HostPort": "8080"
        }
    ]
}
```

می‌فهمی:

```text
Server Port 8080
        |
        ↓
Container Port 80
```

اگر این بخش خالی باشد:

```json
"Ports": null
```

یعنی اصلاً Port Publish نشده.

باید Container را دوباره با:

```bash
docker run -p 8080:80 nginx
```

اجرا کنی.

---

# 3) پیدا کردن Image اشتباه

گاهی فکر می‌کنی Container با Image جدید اجرا شده ولی نشده.

بزن:

```bash
docker inspect mycontainer
```

قسمت:

```json
"Image": "nginx:latest"
```

می‌بینی واقعاً از چه Imageای استفاده کرده.

---

# 4) مشکل Volume و از بین رفتن اطلاعات

مثلاً Database اطلاعاتش نیست.

بررسی:

```bash
docker inspect mysql
```

قسمت:

```json
"Mounts": [
 {
   "Source": "mysql-data",
   "Destination": "/var/lib/mysql"
 }
]
```

می‌فهمی Volume وصل است یا نه.

اگر Mount وجود نداشته باشد، با حذف Container اطلاعات از بین می‌رود.

---

# 5) بررسی Environment Variable

مثلاً برنامه نمی‌تواند به Database وصل شود.

Inspect:

```bash
docker inspect app
```

قسمت:

```json
"Env": [
 "DB_HOST=mysql",
 "DB_PASSWORD=123"
]
```

می‌بینی:

- مقدارها درست هستند؟
    
- اسم Database درست است؟
    

---

# 6) ا-Debug سریع با format

چون خروجی inspect خیلی بزرگ است، فقط چیزی که می‌خواهی می‌گیری.

### گرفتن IP:

```bash
docker inspect -f '{{.NetworkSettings.IPAddress}}' container_name
```

خروجی:

```
172.17.0.2
```

---

### دیدن Volume:

```bash
docker inspect -f '{{.Mounts}}' container_name
```

---

### دیدن Image:

```bash
docker inspect -f '{{.Config.Image}}' container_name
```

---

## Workflow معمول Debug در Docker:

```text
مشکل داری
    |
    ↓
docker ps
    |
    ↓
docker logs container
    |
    ↓
docker inspect container
    |
    ↓
بررسی:
    ├── Network
    ├── Port
    ├── Volume
    ├── Environment
    └── Image
```

پس ذهنیت درست:

- `docker logs` → برنامه چه خطایی داده؟
    
- `docker inspect` → Docker این Container را چطور ساخته و تنظیم کرده؟
    

در کار DevOps، این دو دستور تقریباً همیشه کنار هم استفاده می‌شوند