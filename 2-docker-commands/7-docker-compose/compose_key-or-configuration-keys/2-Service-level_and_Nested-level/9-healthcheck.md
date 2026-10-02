# بریم سراغه کلید healthcheck که یکی از کلید هایه مهم در محیط پروداکشن است که درکنار  depends_on میاد و مکمل اونه در جاهایی که سلامتی وابستگی باید تظمین شه
---
## تعریف کوتاه درمورد کلید healthcheck
ا-`healthcheck` یک **Service-level key** است که برای بررسی سلامت واقعی یک سرویس استفاده می‌شود.

---
## ساختار کلید healthcheck
ساختار کلیش معمولاً اینه:

```
services:
  db:
    image: postgres:16

    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 3s
      retries: 5
      start_period: 10s
```

ساختار درختی:

```
healthcheck
│
├── test
│   └── دستوری که سلامت سرویس را چک می‌کند
│
├── interval
│   └── هر چند وقت یک‌بار تست اجرا شود
│
├── timeout
│   └── هر تست حداکثر چقدر زمان داشته باشد
│
├── retries
│   └── چند بار شکست بخورد تا unhealthy شود
│
└── start_period
    └── ابتدای اجرا چقدر به سرویس فرصت بدهیم آماده شود
```

معنی هرکدام:

`test`  
→ مهم‌ترین بخش است؛ مشخص می‌کند Docker با چه دستوری سلامت سرویس را بررسی کند.

`interval`  
→ فاصله بین دو healthcheck.

مثلاً:

```
interval: 5s
```

یعنی هر ۵ ثانیه یک بار تست کن.

`timeout`

```
timeout: 3s
```

یعنی اگر تست بیشتر از ۳ ثانیه طول کشید، همان تست failed حساب شود.

`retries`

```
retries: 5
```

یعنی اگر چند بار پشت سر هم تست fail شد، سرویس `unhealthy` شود.

`start_period`

```
start_period: 10s
```

یعنی اوایل بالا آمدن سرویس ۱۰ ثانیه فرصت بده و سریع به خاطر fail شدن اولیه unhealthy اعلامش نکن.

در نتیجه جریانش تقریباً اینه:

```
Container start
↓
start_period
↓
test اجرا می‌شود
↓
هر interval دوباره تست
↓
اگر success
→ healthy

اگر چند بار fail
→ retries تمام می‌شود
→ unhealthy
```

برای جزوه‌ات:

ا-**`healthcheck` مشخص می‌کند Docker با چه تستی، با چه فاصله‌ای و با چه تعداد تلاش، سالم یا ناسالم بودن یک سرویس را تشخیص دهد.**

---
## اگر تا الان خوب متوجه شده باشید ساختار health check رو متوجه این میشوید که که مهمترین قسمت درون healthcheck قسمت test است حالا بریم چند تست رو ببنیم 


داخل `test` یک **دستور واقعی** می‌ذاری که داخل خود کانتینر اجرا می‌شه و Docker از نتیجه‌ی اون دستور می‌فهمه سرویس سالمه یا نه.

مثلاً PostgreSQL:

```
healthcheck:
  test: ["CMD-SHELL", "pg_isready -U postgres"]
```

اینجا دستور اصلی اینه:

```
pg_isready -U postgres
```

این ابزار مخصوص PostgreSQL هست و بررسی می‌کنه دیتابیس آماده‌ی قبول connection هست یا نه.

اصل ماجرا **Exit Code** دستوره:

```
دستور test اجرا می‌شود
        ↓
Exit Code = 0
        ↓
تست موفق ✅

Exit Code ≠ 0
        ↓
تست ناموفق ❌
```

مثلاً برای یک Web API ممکنه از `curl` استفاده کنیم:

```
healthcheck:
  test: ["CMD-SHELL", "curl -f http://localhost:5000/health || exit 1"]
```

دستور:

```
curl -f http://localhost:5000/health
```

یعنی از **داخل همان کانتینر** درخواست بزن به:

```
http://localhost:5000/health
```

اگر endpoint درست جواب بده، command موفق می‌شه و exit code `0` می‌ده.

برای سرویس‌های مختلف command فرق می‌کنه:

```
PostgreSQL
→ pg_isready

MySQL
→ mysqladmin ping

Web API
→ curl یا wget

Redis
→ redis-cli ping

Nginx
→ curl http://localhost
```

مثلاً Redis:

```
healthcheck:
  test: ["CMD", "redis-cli", "ping"]
```

اگر Redis سالم باشه:

```
PONG
```

و command با موفقیت تمام می‌شه.

### `CMD` و `CMD-SHELL` هم مهم‌اند

دو مدل رایج نوشتن `test` داری:

```
test: ["CMD", "redis-cli", "ping"]
```

یعنی دستور **مستقیم** اجرا بشه.

ولی:

```
test: ["CMD-SHELL", "curl -f http://localhost:5000/health || exit 1"]
```

یعنی command از طریق shell اجرا بشه؛ بنابراین چیزهایی مثل:

```
||
&&
$VARIABLE
|
```

هم قابل استفاده‌اند.

پس برای جزوه این جمله خیلی مهمه:

ا-**`test` دستوری است که داخل Container اجرا می‌شود و Docker بر اساس Exit Code آن تشخیص می‌دهد Healthcheck موفق بوده یا شکست خورده است.**

و یک نکته‌ی خیلی مهم دیگر: دستوری که در `test` می‌نویسی باید **داخل همان Image وجود داشته باشد**. مثلاً اگر `curl` داخل Image نصب نباشد، healthcheck با `curl` کار نمی‌کند.

---
### اینم یه جدول برایه healthcheck ها برایه سرویس هایه مختلف


|سرویس|تست رایج داخل `healthcheck.test`|معنی|
|---|---|---|
|PostgreSQL|`pg_isready -U postgres`|آماده بودن PostgreSQL برای connection|
|MySQL|`mysqladmin ping -h localhost`|پاسخ‌گو بودن MySQL|
|MariaDB|`mariadb-admin ping -h localhost`|آماده بودن MariaDB|
|Redis|`redis-cli ping`|پاسخ Redis؛ معمولاً `PONG`|
|Nginx|`curl -f http://localhost`|پاسخ دادن وب‌سرور|
|Apache|`curl -f http://localhost`|پاسخ دادن Apache|
|Backend API|`curl -f http://localhost:5000/health`|سالم بودن endpoint سلامت برنامه|
|Node.js API|`curl -f http://localhost:3000/health`|پاسخ دادن API|
|Flask|`curl -f http://localhost:5000/health`|بررسی endpoint برنامه Flask|
|Django|`curl -f http://localhost:8000/health`|بررسی endpoint سلامت Django|
|FastAPI|`curl -f http://localhost:8000/health`|بررسی endpoint سلامت FastAPI|
|Elasticsearch|`curl -f http://localhost:9200/_cluster/health`|وضعیت cluster|
|RabbitMQ|`rabbitmq-diagnostics -q ping`|پاسخ‌گو بودن RabbitMQ|
|MongoDB|`mongosh --eval "db.adminCommand('ping')"`|پاسخ MongoDB|
|Kafka|بسته به Image و setup|معمولاً با ابزارهای Kafka یا TCP check|
|سرویس TCP ساده|`nc -z localhost 8080`|باز بودن پورت TCP|

مثلاً PostgreSQL:

```
healthcheck:
  test: ["CMD-SHELL", "pg_isready -U postgres"]
  interval: 5s
  timeout: 3s
  retries: 5
```

Redis:

```
healthcheck:
  test: ["CMD", "redis-cli", "ping"]
  interval: 5s
  timeout: 3s
  retries: 5
```

Web API:

```
healthcheck:
  test: ["CMD-SHELL", "curl -f http://localhost:5000/health || exit 1"]
  interval: 10s
  timeout: 3s
  retries: 5
```

نکته‌ی خیلی مهم اینه که **برای هر سرویس یک command ثابت جهانی وجود نداره**. بهترین healthcheck دستوریه که واقعاً ثابت کنه سرویس آماده‌ی انجام کار اصلیشه.

مثلاً برای backend، اینکه فقط پورت `5000` باز باشه همیشه کافی نیست؛ بهتره یک endpoint مثل `/health` داشته باشی که اگر برنامه سالم بود `200 OK` بده.
