## بریم سراغه یه قسمت مهم دیگه از قسمت مانت دیتا  بریم سراغه مدل tmpfs
---
ا-`tmpfs` زمانی استفاده می‌شود که بخواهی دیتا **فقط داخل RAM نگه داشته شود** و اصلاً روی Disk ذخیره نشود.

یعنی:

```
RAM
 ↓
tmpfs
 ↓
Container
```

به محض اینکه Container حذف یا Stop شود، دیتای `tmpfs` هم از بین می‌رود.

پس معمولاً برای این موارد خوب است:

- فایل‌های موقت
- ا-cache
- ا-sessionهای موقت
- اطلاعات حساس مثل token یا secret موقت
- جاهایی که سرعت بالاتر از ماندگاری دیتا مهم‌تر است

مثلاً اگر برنامه داخل `/app/cache` فایل موقت تولید کند و نمی‌خواهی روی Disk ذخیره شود:

```
docker run -d \
  --name app \
  --tmpfs /app/cache \
  nginx
```

روش دوم با `--mount` که واضح‌تر و حرفه‌ای‌تر است:

```
docker run -d \
  --name app \
  --mount type=tmpfs,destination=/app/cache \
  nginx
```

می‌توانی محدودیت حجم هم بدهی:

```
docker run -d \
  --name app \
  --mount type=tmpfs,destination=/app/cache,tmpfs-size=100m \
  nginx
```

یعنی حداکثر حدود `100MB` RAM برای این مسیر.

در Docker Compose:

```
services:
  app:
    image: nginx
    tmpfs:
      - /app/cache
```

یا مدل کامل‌تر:

```
services:
  app:
    image: nginx
    tmpfs:
      - /app/cache:size=100m
```

فرق خیلی ساده‌ی سه مدل:

```
Bind Mount
Host path → Container
مناسب source code / config

Volume
Docker storage → Container
مناسب database / persistent data

tmpfs
RAM → Container
مناسب temporary / sensitive / fast data
```

نکته مهم: برای دیتابیس اصلی مثل PostgreSQL از `tmpfs` استفاده نکن، چون با از بین رفتن Container دیتای داخلش هم می‌پرد.

---
### چه قسمت هایی از سرویس هارو میتونیم مانت کنیم با tmpfs
آره. چیزهایی که معمولاً می‌تونی `tmpfs` کنی ایناست:

- ا-**Cache موقت**
  - مثل `/var/cache/nginx`
  - وقتی پاک شدن cache مشکلی ایجاد نمی‌کنه.

- ا-**Temporary files**
  - مثل `/tmp`
  - فایل‌هایی که برنامه فقط موقع اجرا لازم داره.

- ا-**Sessionهای موقت**
  - وقتی session لازم نیست بعد از restart باقی بمونه.

- ا-**Runtime files**
  - فایل‌های PID، socket یا فایل‌های موقت اجرای برنامه.
  - مثلاً بعضی مسیرهای `/run`

- ا-**Sensitive temporary data**
  - ا-token، key یا اطلاعات حساسی که نمی‌خوای روی Disk نوشته بشن و ماندگاری هم لازم ندارن.

- ا-**Build/Test temporary files**
  - در محیط تست یا CI/CD، فایل‌هایی که فقط طی اجرای تست ساخته می‌شن.

- ا-**Temporary upload processing**
  - مثلاً برنامه فایل آپلودشده رو برای چند ثانیه پردازش می‌کنه و بعد حذف می‌کنه.

- ا-**Temporary database برای تست**
  - نه دیتابیس واقعی Production؛ ولی برای integration test می‌تونی دیتای DB رو موقتاً داخل RAM نگه داری.

یک جدول ساده برای یادت موندن:

| Data | tmpfs |
|---|---|
| Nginx cache | ✅ |
| `/tmp` | ✅ |
| temporary session | ✅ |
| temporary token | ✅ |
| runtime files | ✅ |
| test data | ✅ |
| PostgreSQL main data | ❌ |
| source code | ❌ |
| nginx.conf | ❌ |
| فایل‌هایی که باید بعداً باقی بمونن | ❌ |






---
### بریم یه مثال واقعی از این قسمت بزنیم بفهمید که کی از مانت tmpfs استفاده میکنیم

 فقط یه اصلاح مهم: `tmpfs`  می‌تونه برای **اطلاعات حساس** هم مناسب باشه، چون روی دیسک ذخیره نمی‌شه. شرط اصلی اینه که **ماندگاری اطلاعات برامون مهم نباشه**.

 یک مثال واقعی با Nginx:

فرض کن Nginx برای cache از این مسیر استفاده می‌کنه:

```
/var/cache/nginx
```

این cache اگر پاک بشه مشکلی نیست، چون Nginx دوباره می‌سازدش. پس می‌تونیم این مسیر رو `tmpfs` کنیم:

```
docker run -d \
  --name nginx \
  --mount type=tmpfs,destination=/var/cache/nginx \
  nginx
```

حالا ساختار اینطوریه:

```
RAM
 │
 ▼
tmpfs
 │
 ▼
/var/cache/nginx
داخل Container
```

هر چیزی که Nginx داخل `/var/cache/nginx` بنویسه، داخل RAM ذخیره می‌شه.

اگر کانتینر حذف بشه یا سیستم Restart بشه:

```
Cache پاک میشه ✅
```

ولی مشکلی نداریم، چون cache اطلاعات اصلی ما نیست.



```
PostgreSQL Data   ❌ tmpfs
Nginx Cache       ✅ tmpfs
Temporary Files   ✅ tmpfs
Source Code       ❌ tmpfs
Configuration     معمولاً ❌ tmpfs
```

پس بهترین جمله برای به خاطر سپردن:

ا-**tmpfs = دیتایی که موقتیه و اگر پاک شد، برنامه آسیب جدی نمی‌بینه.**
