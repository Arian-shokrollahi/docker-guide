## ا-Docker Volume چیست و چه کاری می‌کند؟

ا-**Volume در Docker برای ذخیره دائمی اطلاعات (Persistent Storage) استفاده می‌شود.**

مشکل اصلی:

```text
Container حذف شود
        ↓
اطلاعات داخل Container هم از بین می‌رود ❌
```

ا-Volume این مشکل را حل می‌کند:

```text
Container
    |
    ↓
 Volume
    |
    ↓
Data باقی می‌ماند ✅
```

---

## مثال واقعی

فرض کن MySQL اجرا می‌کنی:

بدون Volume:

```bash
docker run -d --name mysql mysql
```

ا-Database داخل خود Container ذخیره می‌شود.

اگر:

```bash
docker rm mysql
```

بزنی، اطلاعات Database هم از بین می‌رود.

---

با Volume:

```bash
docker run -d \
--name mysql \
-v mysql-data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql
```

اینجا:

```text
mysql-data  ← Volume
      |
      ↓
/var/lib/mysql ← محل دیتای MySQL داخل Container
```

حالا اگر Container را حذف کنی:

```bash
docker rm mysql
```

Volume باقی می‌ماند و اطلاعات حفظ می‌شود.

---

## دستورات مهم Volume

ساخت Volume:

```bash
docker volume create mydata
```

نمایش Volumeها:

```bash
docker volume ls
```

دیدن جزئیات:

```bash
docker volume inspect mydata
```

حذف Volume:

```bash
docker volume rm mydata
```

پاک کردن Volumeهای بلااستفاده:

```bash
docker volume prune
```

---

## ا-Volume کجا استفاده می‌شود؟

بیشتر برای داده‌هایی که نباید از بین بروند:

✅ Databaseها:

- MySQL
    
- PostgreSQL
    
- MongoDB
    
- Redis
    

✅ فایل‌های آپلود کاربران:

- عکس‌ها
    
- فایل‌ها
    

✅ سرویس‌هایی که تنظیمات و دیتا نگه می‌دارند:

- Jenkins
    
- GitLab
    
- WordPress
    

---

## فرق مهم:

```text
Image
   ↓
Template برنامه

Container
   ↓
اجرای برنامه

Volume
   ↓
ذخیره دائمی اطلاعات
```

خلاصه:

ا-**Container برای اجراست، Volume برای نگهداری دیتا.**
