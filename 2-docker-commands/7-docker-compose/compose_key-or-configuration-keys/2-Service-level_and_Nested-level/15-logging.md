# logging
---
ا-`logging` یک **Service-level key** در Docker Compose است که مشخص می‌کند لاگ‌های آن سرویس **چطور ذخیره، مدیریت یا ارسال شوند**.

مثلاً ساده‌ترین حالت:

```
services:
  app:
    image: my-app
    logging:
      driver: json-file
```

یعنی لاگ‌های کانتینر با `json-file` نگه‌داری شوند؛ این همان درایور رایج Docker است.

می‌توانی محدودیت هم بگذاری:

```
services:
  app:
    image: my-app
    logging:
      driver: json-file
      options:
        max-size: "10m"
        max-file: "3"
```

معنی‌اش:

- ا-`driver` → روش مدیریت لاگ
- ا-`max-size` → هر فایل لاگ حداکثر ۱۰ مگابایت
- ا-`max-file` → حداکثر ۳ فایل لاگ نگه دارد

پس اگر لاگ زیاد شود، Docker فایل‌ها را rotate می‌کند و نمی‌گذارد دیسک بی‌نهایت پر شود.

چند logging driver رایج:

- ا-`json-file` → ذخیره لاگ به شکل JSON
- ا-`local` → درایور محلی Docker با مدیریت بهتر فضا
- ا-`syslog` → ارسال لاگ به syslog
- ا-`journald` → ارسال به systemd journal
- ا-`fluentd` → ارسال به Fluentd
- ا-`gelf` → ارسال به سیستم‌هایی مثل Graylog
- ا-`none` → لاگ ذخیره نشود

برای جزوه:

ا-**`logging` مشخص می‌کند خروجی stdout و stderr کانتینر با چه روشی ذخیره یا ارسال شود و چه محدودیت‌هایی برای حجم لاگ وجود داشته باشد.**

و در production خیلی مهمه، چون اگر برای لاگ‌ها محدودیت نگذاری، ممکنه حجم لاگ‌ها بالا بره و فضای دیسک رو پر کنه.

---
## در محیط پروداکشن معمولا به چه صورت
در production معمولاً دو مدل رایج داری:

1. اگر پروژه کوچک یا متوسط باشد، لاگ‌ها روی خود Host نگه‌داری می‌شوند و حتماً **rotation** می‌گذاریم تا دیسک پر نشود:

```
services:
  backend:
    image: my-backend
    logging:
      driver: json-file
      options:
        max-size: "10m"
        max-file: "5"
```

یعنی هر فایل لاگ حداکثر `10MB` و حداکثر `5` فایل نگه داشته شود.

2. در محیط‌های حرفه‌ای‌تر، لاگ‌ها فقط روی همان سرور نمی‌مانند؛ به یک سیستم مرکزی Logging ارسال می‌شوند، مثلاً:

```
Container
   ↓
stdout / stderr
   ↓
Logging Driver / Agent
   ↓
Central Logging
   ↓
Grafana Loki / Elasticsearch / Graylog / ...
```

در این حالت اگر ۱۰ تا سرور و ۳۰ تا container داشته باشی، لازم نیست تک‌تک وارد سرورها شوی و `docker logs` بزنی؛ همه‌ی لاگ‌ها را از یک جای مرکزی می‌بینی.

یک اصل خیلی مهم production هم اینه که برنامه بهتره لاگ‌ها را روی `stdout` و `stderr` بنویسد، نه اینکه خودش فایل‌های لاگ پراکنده داخل container بسازد. Docker بعداً مدیریت یا ارسال آن‌ها را انجام می‌دهد.

پس برای جزوه:

**در production معمولاً یا Logging Driver را همراه با log rotation تنظیم می‌کنیم، یا لاگ‌های container را به یک سیستم Centralized Logging ارسال می‌کنیم تا مدیریت، جستجو و مانیتورینگ آن‌ها راحت‌تر شود.**
