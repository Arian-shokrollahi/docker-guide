
# intro to environment variable in docker compose file
---
# متغیر محیطی چیست و چه کاربردی دارد درون کامپوس فایل 
متغیر محیطی در Docker Compose یعنی **یک مقدار تنظیماتی که به سرویس یا کانتینر می‌دهیم تا برنامه بداند با چه تنظیماتی اجرا شود، بدون اینکه آن مقدار را داخل کد برنامه هاردکد کنیم.**

مثلاً:

```
services:
  backend:
    environment:
      APP_ENV: production
      DEBUG: "false"
      DB_HOST: database
      DB_PORT: "5432"
      DB_NAME: appdb
```

اینجا این متغیرها هرکدام یک کاربرد دارند:

```
APP_ENV=production
→ برنامه در حالت Production اجرا شود

DEBUG=false
→ حالت Debug خاموش باشد

DB_HOST=database
→ آدرس دیتابیس برای Backend

DB_PORT=5432
→ پورت دیتابیس

DB_NAME=appdb
→ نام دیتابیس
```

پس کاربرد اصلی Environment Variable در Compose این است که **Configuration را از Code جدا کنیم**.

مثلاً به‌جای اینکه داخل Python بنویسی:

```
db_host = "database"
```

می‌نویسی:

```
db_host = os.getenv("DB_HOST")
```

و مقدار واقعی را Compose به برنامه می‌دهد.

یک تعریف خوب برای جزوه‌ات:

> ا-**Environment Variable در Docker Compose متغیری است که تنظیمات Runtime یک سرویس را مشخص می‌کند و باعث می‌شود بتوانیم رفتار برنامه، اطلاعات اتصال به سرویس‌های دیگر و تنظیمات هر محیط را بدون تغییر کد یا Image کنترل کنیم.**

در پروژه واقعی مثلاً:

```
Frontend
   ↓
BACKEND_URL
   ↓
Backend
   ↓
DB_HOST
DB_PORT
DB_NAME
DB_USER
DB_PASSWORD
   ↓
Database
```

یعنی Environment Variableها یکی از ابزارهای اصلی برای **پیکربندی و ارتباط سرویس‌ها** در Compose هستند.

---
## حالا به چند صورت میتونیم ما متغیر محیطی داشته باشیم و اینکه ایا اینطوری است که اون  متغیر هایی که انتخاب میکنیم فقط رویه اون داکر کامپوس قابل  استفاده است یا به کانتینر منتقل میشه

|روش|Compose می‌خواند؟|وارد Container می‌شود؟|
|---|--:|--:|
|`environment:`|بله|بله|
|`.env`|بله|نه، مگر جایی استفاده‌اش کنی|
|`env_file:`|بله|بله|
|Shell مثل `export VAR=...`|بله|نه، مگر در Compose پاسش بدهی|
|`VAR=value docker compose up`|بله|نه، مگر در Compose پاسش بدهی|

---
### حالا بریم سراغه اینکه کدومشون ارزش یاد گیری بیشتری دارند

 بعضی‌ها ارزش یادگیری خیلی بیشتری دارن. برای Production من این ترتیب رو پیشنهاد می‌کنم:

1. ا-**`environment:` — حتماً یاد بگیر**  
    برای تنظیم مستقیم متغیرهای هر سرویس استفاده می‌شود و خیلی رایجه.

```
backend:
  environment:
    APP_ENV: production
    DB_HOST: database
```

این متغیرها وارد Container می‌شوند.

2. ا-**`.env` + `${VAR}` — حتماً یاد بگیر**  
    برای جدا کردن مقدارها از `compose.yaml` خیلی مهمه.

```
APP_ENV=production
DB_HOST=database
```

```
backend:
  environment:
    APP_ENV: ${APP_ENV}
    DB_HOST: ${DB_HOST}
```

این مدل هم در پروژه واقعی زیاد دیده می‌شود. فقط Secretهای حساس را نباید بی‌دقت داخل `.env` و Git نگه داری.

3. ا-**`env_file:` — حتماً یاد بگیر**  
    وقتی یک سرویس متغیرهای زیادی دارد خیلی کاربردی است.

```
backend:
  env_file:
    - backend.env
```

```
APP_ENV=production
DB_HOST=database
DB_PORT=5432
```

متغیرهای `backend.env` وارد Container می‌شوند.

4. ا-**Shell Environment / CI/CD Variables — برای Production خیلی مهم**  
    مثلاً:

```
export DB_PASSWORD=supersecret
docker compose up -d
```

و:

```
environment:
  DB_PASSWORD: ${DB_PASSWORD}
```

این مدل در Production مهم‌تر می‌شود چون Jenkins، GitHub Actions، GitLab CI و سیستم‌های Deployment معمولاً متغیرها را این‌طوری تزریق می‌کنند.

5.ا- **Secrets — وقتی حرفه‌ای‌تر شدی حتماً یاد بگیر**  
    برای اطلاعات حساس مثل:

```
DB_PASSWORD
API_KEY
JWT_SECRET
ACCESS_TOKEN
```

در Production بهتره Secretها را مثل Configuration معمولی مدیریت نکنی.

پس برای مسیر یادگیری خودت:

```
خیلی مهم:
environment:
.env + ${VAR}
env_file:

بعدش:
Shell variables
CI/CD variables

مرحله حرفه‌ای Production:
Secrets / Secret Manager
```

اگر بخوام فقط **سه موردی که الان باید مسلط بشی** رو انتخاب کنم:

```
1. environment:
2. .env + ${VARIABLE}
3. env_file:
```

بعد از اینکه این سه‌تا رو کامل یاد گرفتی، بریم سراغ **اولویت متغیرها (precedence)**؛ یعنی مثلاً اگر یک متغیر هم در `.env` باشد، هم در `env_file` و هم در `environment`، Docker کدام مقدار را انتخاب می‌کند. این بخش واقعاً Production-level است.

---
# و اگر بخواهیم یه مدل رو به عنوان پر استفاده ترین مدل مثال بزنیم در محیط production
اگر بخوام فقط **یک مدل** رو برای Production بگم:

> ا-**`${VARIABLE}` داخل `compose.yaml` + مقداردهی از Environment خود سرور یا CI/CD**

مثلاً:

```
services:
  backend:
    environment:
      DB_HOST: ${DB_HOST}
      DB_USER: ${DB_USER}
      DB_PASSWORD: ${DB_PASSWORD}
```

و مقدارها بیرون از Compose تعریف می‌شن، مثلاً در CI/CD یا محیط سرور:

```
export DB_HOST=database
export DB_USER=appuser
export DB_PASSWORD=supersecret
```

این مدل Production-friendly تره چون:

- مقدارها از Compose جدا می‌مونن
- ا-Secretها داخل فایل Compose نوشته نمی‌شن
- برای dev/staging/production راحت مقدارهای متفاوت می‌دی
- با CI/CD خیلی خوب کار می‌کنه

پس اگر بخوای **فقط یک مدل رو خیلی خوب یاد بگیری**، اینو یاد بگیر:

```
compose.yaml
     ↓
${VARIABLE}
     ↓
Server / CI-CD Environment
     ↓
Container
```

ا-`.env` هم خیلی پرکاربرده، ولی برای Production جدی، معمولاً **Environment سیستم/CI-CD برای Secretها بهتر از نگه‌داشتن همه‌چیز در `.env`** است.

