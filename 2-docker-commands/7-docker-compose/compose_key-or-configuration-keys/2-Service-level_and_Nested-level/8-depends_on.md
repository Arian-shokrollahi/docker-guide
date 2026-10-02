# بریم سراغه کلید بعدی depends_on
---
## دلیل استفاده از `depends_on` چیست و چه فلسفه‌ای دارد؟

- ا-`depends_on` برای مشخص کردن **وابستگی بین سرویس‌ها** در Docker Compose استفاده می‌شود.
    
- با این کلید می‌توان تعیین کرد که قبل از اجرای یک سرویس، کدام سرویس دیگر باید زودتر start شود.
    
- مثلاً اگر `backend` برای کار کردن به `database` نیاز داشته باشد، منطقی است که ابتدا `db` اجرا شود و بعد `backend`.
    
- در این حالت `db` را به‌عنوان سرویس وابسته داخل `depends_on` تعریف می‌کنیم.
    

```
services:
  backend:
    depends_on:
      - db
```

معنی این تنظیم:

```
اول db start شود
↓
بعد backend start شود
```
---
## این کلید depends_on رو معمولا با کلید healthcheck استفاده میکنند :
- چون `depends_on` به‌تنهایی فقط می‌گه **کدوم سرویس زودتر start بشه**، ولی تضمین نمی‌کنه اون سرویس واقعاً **آماده‌ی کار** شده باشه.
- و اینطوری به مشکل میخوریم چون بعضی موقع ها اون سرویس اماده کار نیست و همچی میپیچه تو هم بالا 

---
### خب باید چیکار کنیم؟
- باید بتونیم یه کاری کنیم که سلامت اون سرویس مشخص شود و سپس اون سرویس که بالا اومده اون سرویس وابسته بهش بالا بیاد 
- که این کاررو برایه ما کلید سلامت سنج یا health check انجام میده
---
## پس برایه خلاصه تا اینجا
پس خیلی ساده:

ا-**`depends_on` = ترتیب اجرا**

ا-**`healthcheck` = بررسی آماده بودن واقعی سرویس**

و ترکیبشان می‌شود:

**اول سرویس وابسته را بالا بیاور، صبر کن سالم و آماده شود، بعد سرویس بعدی را اجرا کن.**

---
## بریم سراغه ساختار کلید depends_on که به چه صورته
- ساختار کلید depends_on به دو صورته
- 1-یک اگر بخواهید به صورت خالی استفاده کنید یه ساختار
- 2-و اگر بخواید از مدله شرطی (condition)دار هم استفاده کنید یه مدل دیگه دارد که باید اون ساختار متفاوت رو بلد باشید که  اون وابستگی خودش میشه کلید و یه کلید دارد به اسمه codition که اون هم میتونه سه مقدار داشته باشهه:
- 2-1-->ا-service_started معادل همون حالته خالیه میگه فقط اون سرویس وابسته که در زیر depends_onاومده استارت شده باشه و سلامتش مهم نیست
- 2-2-->ا-service_healthy که این یعنی سرویس وابسته باید healthcheck داشته باشد و سلامتیش تظمین شده باشد
- 2-3-->ا-service_completed_successfully یعنی سرویس وابسته باید کارش را تمام کند و با exit code موفق خارج شود
```
depends_on
│
├── 1) حالت ساده / بدون condition
│   │
│   └── سرویس وابسته به‌صورت لیست می‌آید
│       │
│       └── رفتار ≈ service_started
│           └── فقط باید start شده باشد
│               └── healthy بودن مهم نیست
│
└── 2) حالت شرطی / با condition
    │
    └── نام سرویس وابسته خودش تبدیل به key می‌شود
        │
        └── condition
            │
            ├── service_started
            │   └── سرویس فقط باید start شده باشد
            │
            ├── service_healthy
            │   └── سرویس باید healthcheck داشته باشد
            │       └── و وضعیتش healthy شود
            │
            └── service_completed_successfully
                └── سرویس باید کارش را تمام کند
                    └── و با exit code موفق خارج شود
```
##### ساختارشون به این صورته
- مدل 1 مدل ساده 
```
depends_on:
  - db
```
- مدل 2 مدل که دارایه شرط است
تقریباً معادل:

```
depends_on:
  db:
    condition: service_started
```

و حالت سلامت:

```
depends_on:
  db:
    condition: service_healthy
```

و حالت پایان موفق:

```
depends_on:
  migrate:
    condition: service_completed_successfully
```
---
### بریم سراغه یه مثال واقعی در سطح production
یه مثال خیلی واقعی در production اینه که backend قبل از بالا آمدن، هم به دیتابیس نیاز داشته باشه و هم migration دیتابیس باید کامل شده باشه.

```
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_DB: appdb
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: secret
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U appuser -d appdb"]
      interval: 5s
      timeout: 3s
      retries: 10

  migrate:
    image: my-backend:latest
    command: python manage.py migrate
    depends_on:
      db:
        condition: service_healthy

  backend:
    image: my-backend:latest
    depends_on:
      db:
        condition: service_healthy
      migrate:
        condition: service_completed_successfully
```

فلسفه‌اش اینه:

```
db
↓
اول باید واقعاً healthy شود
↓
migrate
↓
migration باید با موفقیت تمام شود
↓
backend
↓
حالا backend بالا می‌آید
```

اینجا دو نوع `condition` واقعی داریم:

`service_healthy`  
یعنی دیتابیس فقط start نشده باشد؛ واقعاً آماده‌ی گرفتن connection باشد.

`service_completed_successfully`  
یعنی سرویس `migrate` باید کارش را کامل کند و با exit code موفق خارج شود.

این دقیقاً جاییه که `depends_on` از یک ترتیب ساده‌ی start تبدیل میشه به یک orchestration منطقی‌تر برای پروژه‌ی production.
