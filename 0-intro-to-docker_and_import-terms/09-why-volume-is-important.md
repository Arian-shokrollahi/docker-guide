# چرا volume  ها مهم هستند
---
دلیلش این است که Container ذاتاً موقتی است. اگر برنامه‌ای مثل MySQL، PostgreSQL، WordPress یا حتی یک اپلیکیشن با فایل Upload داشته باشی، بدون Volume با حذف یا Recreate شدن Container ممکن است Data را از دست بدهی.

مدل ذهنی ساده:

```
Container = اجرای برنامه
Volume = نگهداری Data
```

---

ا-Volumeها مهم‌اند چون **عمر Data را از عمر Container جدا می‌کنند**.

ا-Container ممکن است حذف، Recreate یا Replace شود، اما اگر داده روی Volume باشد، اطلاعات باقی می‌ماند.

```
Container
   ↓
Application

Volume
   ↓
Persistent Data
```

کاربردهای اصلی Volume:

- نگهداری دیتابیس‌ها مثل MySQL و PostgreSQL
- حفظ فایل‌های Upload شده
- جلوگیری از از بین رفتن Data با حذف Container
- انتقال Data بین Containerهای جدید و قدیمی
- جدا کردن Storage از خود Container

خلاصه‌ی خیلی خوب برای جزوه:

> ا-**Docker Volume باعث می‌شود Data مستقل از Container و به‌صورت Persistent ذخیره شود.**

---
### اگر Volume نداشته باشیچی میشه:
ا-Data داخل خود filesystem کانتینر می‌ماند و با حذف آن Container معمولاً از بین می‌رود.

مثلاً MySQL بدون Volume:

```
docker run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=123456 \
  mysql
```

دیتا داخل خود Container ذخیره می‌شود:

```
Container
   ↓
/var/lib/mysql
   ↓
Database Data
```

اگر بعداً بزنی:

```
docker rm -f mysql-db
```

آن Container و دیتای داخلش حذف می‌شوند. ❌

ولی با Volume:

```
docker run -d \
  --name mysql-db \
  -v mysql-data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=123456 \
  mysql
```

ساختار می‌شود:

```
Container
   ↓
/var/lib/mysql
   ↓
mysql-data Volume
```

حالا اگر Container حذف شود:

```
Container ❌
Volume ✅
Data ✅
```

پس خلاصه:

**بدون Volume، Data به عمر Container وابسته است. با Volume، Data مستقل و Persistent می‌شود.**
