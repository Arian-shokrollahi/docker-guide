## دستور `docker volume inspect` چیست؟

```bash
docker volume inspect
```

برای **دیدن جزئیات کامل یک Volume خاص** استفاده می‌شود.

یعنی:

> ا-Docker، این Volume کجاست؟ با چه Driverای ساخته شده؟ چه اطلاعاتی دارد؟

---

## ساختار دستور:

```bash
docker volume inspect VOLUME_NAME
```

مثال:

```bash
docker volume inspect mysql-data
```

---

## مثال خروجی:

```json
[
    {
        "CreatedAt": "2026-09-25T10:30:00Z",
        "Driver": "local",
        "Mountpoint": "/var/lib/docker/volumes/mysql-data/_data",
        "Name": "mysql-data",
        "Scope": "local"
    }
]
```

---

## بررسی خروجی:

### 1) Name

```json
"Name": "mysql-data"
```

نام Volume است.

---

### 2) Driver

```json
"Driver": "local"
```

یعنی Docker خودش Volume را روی همین سیستم مدیریت می‌کند.

---

### 3) Mountpoint

```json
"Mountpoint": "/var/lib/docker/volumes/mysql-data/_data"
```

مسیر واقعی ذخیره اطلاعات Volume روی Host است.

یعنی دیتا اینجا نگهداری می‌شود.

---

### 4) CreatedAt

```json
"CreatedAt": "2026-09-25..."
```

زمان ساخته شدن Volume.

---

### 5) Scope

```json
"Scope": "local"
```

یعنی این Volume فقط روی همین Docker Host قابل استفاده است.

---

## سناریوی واقعی

فرض کن:

```bash
docker volume create mysql-data
```

بعد:

```bash
docker run -d \
--name mysql \
-v mysql-data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql
```

حالا:

```bash
docker volume inspect mysql-data
```

به تو می‌گوید:

```text
Volume:
mysql-data

متصل به:
Container → /var/lib/mysql

ذخیره شده در:
/var/lib/docker/volumes/mysql-data/_data
```

---

## در Debug چه کمکی می‌کند؟

مثلاً Database اطلاعاتش را پیدا نمی‌کند.

با:

```bash
docker volume inspect mysql-data
```

بررسی می‌کنی:

- ا-Volume واقعاً وجود دارد؟
    
- اسم درست است؟
    
- مسیر ذخیره کجاست؟
    
- ا-Driver چیست؟
    

---

خلاصه:

```text
docker volume ls
        ↓
دیدن لیست Volumeها

docker volume inspect
        ↓
بررسی جزئیات یک Volume مشخص
```

ا-`volume ls` می‌گوید **چه Volumeهایی داری**،  
ا-`volume inspect` می‌گوید **داخل Docker این Volume دقیقاً چگونه تنظیم شده است**.
