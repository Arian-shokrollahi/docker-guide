# profiles
---
ا-`profiles` یک **Service-level key** در Docker Compose است که برای این استفاده می‌شود که بعضی سرویس‌ها **همیشه بالا نیایند** و فقط وقتی یک profile مشخص را فعال کردی اجرا شوند.

مثلاً:

```
services:
  app:
    image: my-app

  debug:
    image: alpine
    profiles:
      - debug
```

اینجا `app` همیشه بالا می‌آید، ولی `debug` فقط وقتی profile مربوطه را فعال کنی.

مثلاً:

```
docker compose --profile debug up
```
ا-<mark>
فلسفه‌اش اینه که یک Compose file داشته باشی ولی سرویس‌های اختیاری رو بر اساس نیاز روشن کنی.</mark>

مثلاً برای این سناریوها خیلی کاربردیه:

- `debug`
- `development`
- `testing`
- `monitoring`
- ابزارهای admin

مثال:

```
services:
  app:
    image: my-app

  db:
    image: postgres

  adminer:
    image: adminer
    profiles:
      - tools
```

حالت عادی:

```
docker compose up
```

فقط:

```
app
db
```

ولی:

```
docker compose --profile tools up
```

این‌ها بالا می‌آیند:

```
app
db
adminer
```

پس برای جزوه:

ا-**`profiles` مشخص می‌کند یک سرویس فقط در چه حالت یا سناریوی خاصی فعال شود؛ سرویس بدون profile معمولاً به‌صورت عادی اجرا می‌شود، ولی سرویس profileدار فقط با فعال کردن آن profile بالا می‌آید.**

---
## یعنی با اضافه کردن درون کامپوس فایل ها میشه از اون به بعد به  عنوان  فلگ استفاده کنی در دستور docker compose  و اون سرویس دلخواتو بیاری بالا نه همشو
وقتی داخل Compose برای یک سرویس `profiles` تعریف می‌کنی، بعد موقع اجرای `docker compose` می‌تونی با فلگ `--profile` مشخص کنی کدام سرویس‌های اختیاری فعال شوند.

مثلاً:

```
services:
  app:
    image: my-app

  adminer:
    image: adminer
    profiles:
      - tools
```

اجرای عادی:

```
docker compose up
```

فقط `app` بالا میاد.

ولی اگر بنویسی:

```
docker compose --profile tools up
```

هم `app` و هم `adminer` بالا میان.

پس ذهنی این‌طوری حفظ کن:

```
profiles داخل compose file
        ↓
اسم یک حالت اختیاری
        ↓
--profile در دستور docker compose
        ↓
فعال شدن سرویس‌های مربوط به آن profile
```

یعنی بله، عملاً اسم profile رو بعداً به‌صورت ورودی/فلگ به دستور Compose می‌دی.
