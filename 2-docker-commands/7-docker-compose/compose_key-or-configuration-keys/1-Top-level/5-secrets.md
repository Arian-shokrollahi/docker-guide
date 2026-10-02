# بریم سراغه کلید بعدی کلید secrets
---
## اول کلید secrets برایه چی استفاده میشه
`secrets` برای نگهداری و در اختیار گذاشتن **اطلاعات حساس** به سرویس‌هاست؛ چیزهایی که بهتره داخل `environment` یا مستقیم داخل Compose ننویسی.

مثلاً:

- پسورد دیتابیس
- ا-API Key
- ا-Token
- ا-Certificate key
---
## یه نمونه مثال ازش
مثال:

```
services:
  app:
    image: my-app
    secrets:
      - db-password

secrets:
  db-password:
    file: ./db_password.txt
```
- شما در سرویس لول فقط اسمی که برایه اون مسیر secrects انتخاب کردید رو میزارید
اینجا:

- ا-Top-level `secrets` → خود secret را تعریف می‌کنی
- ا-Service-level `secrets` → مشخص می‌کنی کدام سرویس به آن secret دسترسی داشته باشد

برای جزوه:

ا-**`secrets` زمانی استفاده می‌شود که بخواهیم اطلاعات حساس را جدا از تنظیمات عادی نگه داریم و فقط در اختیار سرویس‌های لازم قرار بدهیم.**

---
## حالا یه سوال اساسی ما در configs اگر اسم مستعاری هم میدادیم یه مقصدی بود که از اونجا قابل دسترسی بود اون فایل کانفیگ حالا این کلید که مقصد ندارد در کجا قابل دسترسه

داخل کانتینر، Secret معمولاً به‌صورت یک **فایل** قابل دسترسیه، نه Environment Variable.

مثلاً:

```
services:
  app:
    secrets:
      - db-password

secrets:
  db-password:
    file: ./db_password.txt
```

داخل کانتینر معمولاً اینجا می‌بینیش:

```
/run/secrets/db-password
```

یعنی اگر وارد کانتینر بشی:

```
docker exec -it app sh
```

بعد:

```
ls /run/secrets
```

می‌بینی:

```
db-password
```

و برای دیدن محتوا:

```
cat /run/secrets/db-password
```

نکته مهم: `source` اسم Secretای است که در Top-level تعریف کردی؛ مسیر واقعی روی Host داخل `file:` می‌آید. داخل کانتینر هم معمولاً Secret در `/run/secrets/<secret-name>` قرار می‌گیرد.

مثلاً با long syntax:

```
services:
  app:
    secrets:
      - source: db-password
        target: my-password

secrets:
  db-password:
    file: ./db_password.txt
```

داخل کانتینر می‌شود:

```
/run/secrets/my-password
```

پس فرمولش:

`file` = مسیر روی Host  
`source` = اسم Secret  
`target` = اسم/مسیر Secret داخل Container
