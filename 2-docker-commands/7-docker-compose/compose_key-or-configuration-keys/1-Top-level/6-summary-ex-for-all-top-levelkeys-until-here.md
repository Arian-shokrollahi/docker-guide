# بریم سراغه یه مثال که همه کلید هایه سطح بالایی که تا الان یاد گرفتیم رو نشون بده
---
# میخوایم یه compose  فایلی درست کنیم با این ساختار

سناریو:

یک پروژه داری با دو سرویس:

- `app`
- `db`

شرایط:

- `app` باید به شبکه `app-net` وصل باشد.
- `db` هم باید به همان شبکه `app-net` وصل باشد.
- برای دیتابیس یک Named Volume به اسم `db-data` داشته باش که اطلاعات دیتابیس داخلش ذخیره شود.
- برای `app` یک config به اسم `app-config` داشته باش که از فایل `./app.conf` روی Host بیاید و داخل کانتینر در مسیر `/app/config/app.conf` قرار بگیرد.
- برای `app` یک secret به اسم `db-password` داشته باش که از فایل `./db_password.txt` روی Host بیاید و داخل کانتینر در مسیر پیش‌فرض Secret قرار بگیرد.
- فقط از این Top-level keyها استفاده کن:  
    `services`  
    `volumes`  
    `networks`  
    `configs`  
    `secrets`

برای سرویس‌ها هم می‌تونی از `image` و زیرکلیدهای لازم استفاده کنی.

هدفت اینه که ساختار نهایی تقریباً این رابطه رو داشته باشه:

```
app
 ├── network → app-net
 ├── config  → app-config
 └── secret  → db-password

db
 ├── network → app-net
 └── volume  → db-data
```
---
### کد هایه سناریویی که گفتیم
پس نسخه‌ی اصلاح‌شده:

```
services:
  app:
    image: alpine
    networks:
      - app-net
    configs:
      - source: app-config
        target: /app/config/app.conf
    secrets:
      - db-password

  db:
    image: postgres
    networks:
      - app-net
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:

networks:
  app-net:

configs:
  app-config:
    file: ./app.conf

secrets:
  db-password:
    file: ./db_password.txt
```
- بهتون یه توضیحی کوتاهی هم بدم که اتفاقی میوفته درون این کانفیگ فایل:

- ۱-اول در قسمت کلید سطح بالا services سرویس هامون که app db بودن رو تعریف کردیم

- ۲- در سرویس لول app اومدیم براش شبکه انتخاب کردیم که عضو کدوم شبکه باشه
- اون فایل کانفیگش که در مبدا اسمی است برایه اون مسیر کانفیگ در کلید سطح بالا اومدیم تعریف کردیم اون فایل کانفیگ در کجا مقصد در دسترس باشه 
- بعد اومدیم یه secrets تعریف کردیم که اسم مستعاری است برایه اون مسیر secrets که در لول بالا تعریف میشه و به این دلیل در لول سرویس بهش مقصد نمیدیم چون میره تو مسیره run/secret/ فایل هایه کلید secret


- ۳- حالا میریم سراغه سرویس بعدی که برایه سرویس بعدی اومدیم گفتیم از کدوم image ساخته شه 
- عضو کدوم شبکه
- داده ش در مسیر db-data والیوم که با inspect میتونیم ببنیم source کجاست ذخیره شه

- ۴- در اخر اومدیم اون کلیید هایی که در سرویس لول تعریف کردیم رو در لول بالا هم تعریف کردیم


- ۵-یه نکته: <mark> همه کلید هایه سطح بالا هم باید در سرویس لول هم در تاپ لول تعریف شن به جز services چون سرویس فقط سرویس هارو تعریف میکنه و نیازی نداره که در تاپ لول هم دوباره تعریف شه</mark>

```
Top-level
db-data       → تعریف Volume
app-net       → تعریف Network
app-config    → تعریف Config
db-password   → تعریف Secret

Service-level
volumes       → استفاده از Volume
networks      → اتصال به Network
configs       → استفاده از Config
secrets       → استفاده از Secret
```
