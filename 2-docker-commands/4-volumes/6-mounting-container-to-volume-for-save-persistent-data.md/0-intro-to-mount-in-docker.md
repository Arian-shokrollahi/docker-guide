 در Docker وقتی می‌گوییم **Mount کردن** یعنی یک منبع ذخیره‌سازی را به یک مسیر داخل Container وصل کنیم.

به طور کلی 3 مدل اصلی Mount داریم:

```
1) Named Volume
2) Bind Mount
3) tmpfs Mount
```

و برای نوشتن Mount هم دو Syntax مهم داریم:

```
-v
--mount
```

`-v` کوتاه‌تر و قدیمی‌تر است.  
`--mount` خواناتر و برای کار حرفه‌ای بهتر است.

---

# 1) Named Volume

ا-Docker خودش Volume را مدیریت می‌کند.

```bash
# ساخت Volume
docker volume create mydata

# اتصال Volume به Container با -v
docker run -d \
  --name web \
  -v mydata:/app/data \
  nginx
```

معنی:

```bash
mydata
   ↓
Volume مدیریت‌شده توسط Docker

/app/data
   ↓
مسیر داخل Container
```

همین با `--mount`:

```bash
# اتصال Named Volume با syntax خواناتر
docker run -d \
  --name web \
  --mount source=mydata,target=/app/data \
  nginx
```

کاربرد:

- ا-Database
- ا-Persistent Data
- فایل‌هایی که نباید با حذف Container از بین بروند

---

# 2) Bind Mount

اینجا به جای Volume، یک **مسیر واقعی از Host** را مستقیم به Container وصل می‌کنی.

```bash
# اتصال یک پوشه Host به Container
docker run -d \
  --name web \
  -v /home/ali/project:/usr/share/nginx/html \
  nginx
```

یعنی:

```bash
Host:
/home/ali/project
        ↓
Container:
/usr/share/nginx/html
```

با `--mount`:

```bash
# Bind Mount با syntax جدیدتر
docker run -d \
  --name web \
  --mount type=bind,source=/home/ali/project,target=/usr/share/nginx/html \
  nginx
```

کاربرد:

- ا-Development
- سورس‌کد پروژه
- ا-Config file
- وقتی می‌خواهی تغییرات Host فوراً داخل Container دیده شود

---

# 3) tmpfs Mount

داده فقط داخل RAM نگهداری می‌شود.

```bash
# ساخت tmpfs mount
docker run -d \
  --name app \
  --tmpfs /app/temp \
  nginx
```

یا با `--mount`:

```bash
# tmpfs با syntax جدید
docker run -d \
  --name app \
  --mount type=tmpfs,target=/app/temp \
  nginx
```

کاربرد:

- داده موقت
- اطلاعات حساس
- ا-Cache موقت

نکته:

```
Container Stop/Delete
      ↓
tmpfs Data از بین می‌رود
```

---

# تفاوت سریع

|نوع|محل ذخیره|Persistent؟|کاربرد|
|---|---|---|---|
|Named Volume|مدیریت توسط Docker|✅|Database / Production|
|Bind Mount|مسیر مشخص Host|✅|Development / Config|
|tmpfs|RAM|❌|Temporary / Sensitive Data|

---

# Read-only Mount

برای اینکه Container فقط بتواند فایل را بخواند:

```
# Named Volume فقط خواندنی
docker run -v mydata:/app/data:ro nginx
```

یا:

```
# Read-only با --mount
docker run \
  --mount source=mydata,target=/app/data,readonly \
  nginx
```

---

پس مدل ذهنی خیلی مهم:

```
Mount
├── Named Volume  → Docker-managed storage
├── Bind Mount    → Host path
└── tmpfs         → RAM
```

و Syntax:

```
-v       → کوتاه‌تر
--mount  → واضح‌تر و حرفه‌ای‌تر
```

برای مسیر یادگیری تو فعلاً روی **Named Volume + Bind Mount + تفاوت `-v` و `--mount`** تمرکز کن؛ این‌ها بیشترین استفاده را دارند.
