## دستور `docker volume create` چیست؟

```bash
docker volume create
```

برای **ساختن یک Volume جدید در Docker** استفاده می‌شود.

یعنی به Docker می‌گویی:

> یک فضای ذخیره‌سازی دائمی بساز که بعداً بتوانم به Container وصل کنم.

---

## ساختار دستور:

```bash
docker volume create VOLUME_NAME
```

مثال:

```bash
docker volume create mysql-data
```

ا-Docker یک Volume با نام `mysql-data` می‌سازد.

---

## بررسی اینکه ساخته شده:

```bash
docker volume ls
```

خروجی:

```text
DRIVER    VOLUME NAME
local     mysql-data
```

---

## استفاده واقعی

فرض کن می‌خواهی MySQL اجرا کنی و اطلاعاتش با حذف Container از بین نرود.

### مرحله ۱: ساخت Volume

```bash
docker volume create mysql-data
```

---

### مرحله ۲: اتصال Volume به Container

```bash
docker run -d \
--name mysql-db \
-v mysql-data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql
```

اینجا:

```text
mysql-data
      |
      ↓
/var/lib/mysql داخل Container
```

یعنی اطلاعات Database داخل Volume ذخیره می‌شود.

---

## قبل از Volume:

```text
Container
   |
   ↓
Database files

docker rm
   |
   ↓
Data حذف می‌شود ❌
```

---

## بعد از Volume:

```text
Container
   |
   ↓
Volume
   |
   ↓
Database files

docker rm
   |
   ↓
Data باقی می‌ماند ✅
```

---

## دیدن اطلاعات Volume:

```bash
docker volume inspect mysql-data
```

مثلاً:

```json
[
 {
  "Name": "mysql-data",
  "Driver": "local",
  "Mountpoint": "/var/lib/docker/volumes/mysql-data/_data"
 }
]
```

---

## نکته مهم:

اگر هنگام `docker run` اسم Volume بدهی و وجود نداشته باشد، Docker خودش آن را می‌سازد:

مثلاً:

```bash
docker run -v mydata:/app/data nginx
```

اگر `mydata` وجود نداشته باشد، Docker آن را Create می‌کند.

ولی وقتی می‌خواهی جداگانه مدیریت کنی:

```bash
docker volume create mydata
```

استفاده می‌کنی.

---

خلاصه:

```text
docker volume create
          ↓
ساخت فضای ذخیره‌سازی دائمی

docker volume ls
          ↓
دیدن Volumeها

docker run -v
          ↓
وصل کردن Volume به Container
```

ا-Volume بیشتر در پروژه‌های واقعی برای **Database، فایل‌های آپلودی و هر دیتایی که نباید با حذف Container از بین برود** استفاده می‌شود.
