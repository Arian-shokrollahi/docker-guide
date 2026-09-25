## دستور `docker volume ls` چیست و چه کاری می‌کند؟

```bash
docker volume ls
```

برای **لیست کردن تمام Volumeهایی که در Docker روی سیستم ساخته شده‌اند** استفاده می‌شود.

یعنی Docker را بررسی می‌کنی که:

> چه فضای ذخیره‌سازی دائمی (Volume) الان وجود دارد؟

---

## مثال واقعی

فرض کن قبلاً یک MySQL با Volume اجرا کرده‌ای:

```bash
docker run -d \
--name mysql-db \
-v mysql-data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql
```

ا-Docker یک Volume به نام `mysql-data` می‌سازد.

حالا می‌زنی:

```bash
docker volume ls
```

خروجی:

```text
DRIVER    VOLUME NAME
local     mysql-data
```

---

## بررسی خروجی

### ستون اول:

```text
DRIVER
```

نوع Volume Driver را نشان می‌دهد.

اینجا:

```text
local
```

یعنی:

ا-Volume روی همین سیستم Docker ساخته شده و Docker خودش مدیریت می‌کند.

---

### ستون دوم:

```text
VOLUME NAME
```

اسم Volume است.

اینجا:

```text
mysql-data
```

یعنی Volumeای که برای ذخیره اطلاعات MySQL ساخته شده.

---

تصویر ذهنی:

```text
docker volume ls

        ↓

Docker Engine

        ↓

Volume ها:

+----------------+
| mysql-data     |
| redis-data     |
| app-uploads    |
+----------------+
```

---

## یک مثال دیگر

فرض کن چند سرویس داری:

```bash
docker volume ls
```

خروجی:

```text
DRIVER    VOLUME NAME
local     postgres_data
local     wordpress_files
local     redis_cache
```

معنی:

```
postgres_data
        ↓
اطلاعات PostgreSQL


wordpress_files
        ↓
فایل‌های WordPress


redis_cache
        ↓
اطلاعات Redis
```

---

## اگر هیچ Volumeای نداشته باشی:

خروجی ممکن است فقط Header باشد:

```text
DRIVER    VOLUME NAME
```

یعنی:

ا-Volumeای ساخته نشده.

---

## بعد از `docker volume ls` معمولاً چه کار می‌کنیم؟

اگر یک Volume دیدی و خواستی اطلاعاتش را ببینی:

```bash
docker volume inspect mysql-data
```

خروجی جزئی‌تر:

```json
[
 {
  "Name": "mysql-data",
  "Driver": "local",
  "Mountpoint": "/var/lib/docker/volumes/mysql-data/_data"
 }
]
```

اینجا می‌فهمی:

- اسم Volume چیست
    
- توسط چه Driverای ساخته شده
    
- فایل‌هایش کجا نگهداری می‌شوند
    

---

پس خلاصه:

```text
docker volume ls
        |
        ↓
نمایش Volumeهای موجود

Volume
        |
        ↓
ذخیره دائمی Data خارج از Container
```

در یک پروژه واقعی، وقتی یک Database بالا می‌آوری، یکی از اولین چیزهایی که بررسی می‌کنی همین است که آیا Volume درست ساخته و وصل شده یا نه.