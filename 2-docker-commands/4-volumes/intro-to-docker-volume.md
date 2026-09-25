# ا-Docker Volume چیست؟

<p align="center">
	<img src="00-images/introvolume.png" alt="" width=1000>
</p>



ا-**Volume** در Docker برای **ذخیره دائمی داده‌ها خارج از چرخه عمر Container** استفاده می‌شود.

مشکل اصلی:

```text
Container حذف شود → اطلاعات داخلش حذف می‌شود
```

ا-Volume این مشکل را حل می‌کند:

```text
Container
    |
    ↓
 Volume
    |
    ↓
Data باقی می‌ماند
```

یعنی حتی اگر Container را حذف و دوباره بسازی، داده‌ها باقی می‌مانند.

---

# ساختار Volume

ساخت Volume:

```bash
# ساخت یک Volume جدید
docker volume create mydata
```

دیدن Volumeها:

```bash
# نمایش Volume های موجود
docker volume ls
```

دیدن جزئیات:

```bash
# نمایش اطلاعات Volume
docker volume inspect mydata
```

حذف Volume:

```bash
# حذف Volume
docker volume rm mydata
```

---

# استفاده از Volume در Container

ساختار اصلی:

```bash
docker run -v VOLUME_NAME:CONTAINER_PATH IMAGE
```

مثال:

```bash
# اجرای MySQL با Volume برای ذخیره Database
docker run -d \
--name mysql-db \
-v mysql-data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql
```

توضیح:

```text
mysql-data
      |
      ↓
Volume روی Host

/var/lib/mysql
      |
      ↓
مسیر ذخیره Database داخل Container
```

---

# سناریوی واقعی

بدون Volume:

```text
Container MySQL
        |
        ↓
Database داخل Container

docker rm
        |
        ↓
اطلاعات حذف می‌شود ❌
```

با Volume:

```text
Container MySQL
        |
        ↓
Volume
        |
        ↓
Database محفوظ می‌ماند ✅
```

---

# ا-Volume در چه جاهایی استفاده می‌شود؟

### ا-Databaseها (مهم‌ترین کاربرد)

مثل:

- MySQL
    
- PostgreSQL
    
- MongoDB
    
- Redis
    

چون اطلاعات باید باقی بماند.

---

### فایل‌های Upload شده

مثلاً:

```text
User uploads
     |
     ↓
Volume
     |
     ↓
Container
```

---

### ا-Configuration و Data برنامه‌ها

مثل:

- WordPress
    
- Jenkins
    
- GitLab
    
- Elasticsearch
    

---

# ا-Bind Mount در مقابل Volume

دو روش برای Mount کردن داده داریم:

### Volume:

```bash
docker run -v mydata:/app/data nginx
```

Docker خودش مدیریت می‌کند.

---

### Bind Mount:

```bash
docker run -v /home/user/project:/app nginx
```

خودت مسیر Host را مشخص می‌کنی.

---

# نکات مهم DevOps

✅ ا-Volume مستقل از Container است.  
✅ حذف Container باعث حذف Volume نمی‌شود.  
✅ برای Production معمولاً Databaseها حتماً Volume دارند.  
✅ ا-Volumeها توسط Docker مدیریت می‌شوند.

خلاصه:

```text
Image
  |
  ↓
Container
  |
  ↓
Volume
  |
  ↓
Data دائمی
```

ا-**Container برای اجرای برنامه است، Volume برای نگهداری اطلاعات.**
