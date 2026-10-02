# بریم سراغه کلید کانفیگ environment
---
حتماً.

### مقدمه `environment`

ا-`environment` یک **Service-level key** در Docker Compose است که با آن متغیرهای محیطی را وارد Container می‌کنیم.

این متغیرها معمولاً برای **تنظیم رفتار برنامه بدون تغییر سورس‌کد** استفاده می‌شوند.

مثلاً به‌جای اینکه داخل کد بنویسی:

```
port = 5000
```

می‌توانی مقدار را از Environment Variable بگیری:

```
PORT=5000
```

و در Compose:

```
services:
  backend:
    image: my-api
    environment:
      PORT: 5000
```

پس مسیر کلی این است:

```
Docker Compose
     ↓
environment
     ↓
Container
     ↓
Application
```

---

### پرکاربردترین Environment Variableها

اسم Environment Variableها ثابت و اجباری نیست؛ **هر برنامه مشخص می‌کند چه متغیرهایی را پشتیبانی می‌کند**. اما این اسم‌ها در پروژه‌های واقعی خیلی رایج‌اند:

|Environment|کاربرد|
|---|---|
|`APP_ENV`|مشخص کردن محیط اجرا مثل development / production|
|`NODE_ENV`|محیط اجرای برنامه‌های Node.js|
|`DEBUG`|فعال/غیرفعال کردن Debug|
|`PORT`|پورتی که برنامه روی آن Listen می‌کند|
|`HOST`|آدرس Listen برنامه|
|`DB_HOST`|آدرس یا نام سرویس Database|
|`DB_PORT`|پورت Database|
|`DB_NAME`|نام Database|
|`DB_USER`|نام کاربری Database|
|`DB_PASSWORD`|رمز Database|
|`DATABASE_URL`|کل Connection String دیتابیس|
|`REDIS_HOST`|آدرس Redis|
|`REDIS_PORT`|پورت Redis|
|`REDIS_PASSWORD`|پسورد Redis|
|`API_URL`|آدرس یک API دیگر|
|`BACKEND_URL`|آدرس Backend برای Frontend|
|`LOG_LEVEL`|سطح Log|
|`TZ`|Time Zone|
|`SECRET_KEY`|کلید داخلی برنامه|
|`API_KEY`|API Key سرویس خارجی|

### مهم‌ترین‌هایی که پیشنهاد می‌کنم اول یاد بگیری

برای پروژه‌های چندسرویسی Docker، این‌ها خیلی مهم‌اند:

```
APP_ENV
PORT

DB_HOST
DB_PORT
DB_NAME
DB_USER
DB_PASSWORD

REDIS_HOST
REDIS_PORT

API_URL
LOG_LEVEL
```

مثلاً یک Backend واقعی:

```
services:

  backend:
    image: my-backend:v1

    environment:
      APP_ENV: production
      PORT: 5000

      DB_HOST: db
      DB_PORT: 5432
      DB_NAME: shop
      DB_USER: admin
      DB_PASSWORD: strongpassword

      REDIS_HOST: redis
      REDIS_PORT: 6379

      LOG_LEVEL: info
```

اینجا می‌تونی Environmentها رو چند دسته ببینی:

```
Application
├── APP_ENV
├── PORT
└── LOG_LEVEL

Database
├── DB_HOST
├── DB_PORT
├── DB_NAME
├── DB_USER
└── DB_PASSWORD

Redis
├── REDIS_HOST
└── REDIS_PORT
```

### یک نکته خیلی مهم

مثلاً:

```
DB_HOST: db
```

`db` یک کلمه تصادفی نیست.

اگر سرویس دیتابیس این باشه:

```
services:

  backend:
    environment:
      DB_HOST: db

  db:
    image: postgres:16
```

اسم سرویس:

```
db
```

در Docker Network مثل hostname عمل می‌کنه.

پس Backend می‌تواند به:

```
db:5432
```

وصل شود.

### جمع‌بندی برای جزوه

> ا-`environment` برای ارسال تنظیمات و متغیرهای محیطی به داخل Container استفاده می‌شود تا رفتار Application را بدون تغییر دادن سورس‌کد کنترل کنیم.

پراستفاده‌ترین دسته‌ها:

```
Application Configuration
APP_ENV
PORT
DEBUG
LOG_LEVEL

Database
DB_HOST
DB_PORT
DB_NAME
DB_USER
DB_PASSWORD

Other Services
REDIS_HOST
REDIS_PORT
API_URL

System
TZ
```

فقط حواست باشه `DB_HOST` یا `PORT` و امثال این‌ها **کلیدهای خود Docker Compose نیستند**؛ اسم Environment Variableهایی هستند که Application یا Image موردنظر انتظار دارد.

---
## بریم سراغه یه مثال در محیط پروداکشن
حتماً. این یک مثال ساده و واقعی برای محیط Production است:

```
services:
  backend:
    image: my-backend:v1

    environment:
      APP_ENV: production
      DEBUG: "false"
      PORT: "5000"

      DB_HOST: db
      DB_PORT: "5432"
      DB_NAME: shop_prod
      DB_USER: shop_admin
      DB_PASSWORD: strong_password

      REDIS_HOST: redis
      REDIS_PORT: "6379"

      LOG_LEVEL: info

    ports:
      - "8000:5000"

    depends_on:
      - db
      - redis

  db:
    image: postgres:16

    environment:
      POSTGRES_DB: shop_prod
      POSTGRES_USER: shop_admin
      POSTGRES_PASSWORD: strong_password

    volumes:
      - postgres-data:/var/lib/postgresql/data

  redis:
    image: redis:7

volumes:
  postgres-data:
```

تحلیل `backend`:

```
APP_ENV: production
```

یعنی برنامه بفهمد در محیط Production اجرا می‌شود.

```
DEBUG: "false"
```

یعنی Debug خاموش باشد.

```
PORT: "5000"
```

یعنی برنامه داخل Container روی پورت `5000` اجرا شود.

```
DB_HOST: db
```

یعنی Backend برای دیتابیس به سرویس `db` وصل شود.

```
DB_PORT: "5432"
```

پورت PostgreSQL داخل Docker network.

```
DB_NAME: shop_prod
DB_USER: shop_admin
DB_PASSWORD: strong_password
```

اطلاعات اتصال به دیتابیس.

```
REDIS_HOST: redis
REDIS_PORT: "6379"
```

Backend به سرویس Redis وصل می‌شود.

```
LOG_LEVEL: info
```

در Production معمولاً Logها را کنترل‌شده‌تر نگه می‌داریم.

نکته خیلی مهم اینه که این قسمت:

```
backend:
  environment:
    DB_HOST: db
    DB_PORT: "5432"
```

باید با سرویس دیتابیس هماهنگ باشه:

```
db:
  image: postgres:16
```

یعنی مسیر ارتباط:

```
backend
   ↓
DB_HOST=db
   ↓
db:5432
   ↓
PostgreSQL
```

و از سمت بیرون:

```
Host:8000
   ↓
Container backend:5000
```

در Production واقعی بهتره پسوردها رو مستقیم داخل Compose ننویسی و از `.env` یا `secrets` استفاده کنی.
