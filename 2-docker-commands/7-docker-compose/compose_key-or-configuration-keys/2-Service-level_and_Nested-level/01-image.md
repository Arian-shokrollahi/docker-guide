# بریم سراغه اولین کلید کانفیگ image
---
## کلید کانفیگ یا کلید کامپوس image چیست؟
کلید `image` در Docker Compose مشخص می‌کند سرویس از **کدام Docker Image** برای ساخت container استفاده کند.
مثال خیلی ساده:

```
services:
  web:
    image: nginx:latest
```

یعنی:

```
image: nginx:latest
       └──┬──┘ └─┬─┘
        name     tag
```
---
### باید حتما اون ایمیج رو از قبل دانلود کرده باشیم:
- ۱-اگر دانلود داشته باشیم ---> استفاده میشه
- ۲-اگر دانلود نداشته باشیم--> ار ریجیستری دانلود میشه 
---
### یه مثال  ار کانفیگ فایل و اینکه متوجه tag  و name اون ایمیج بشید
مثلاً:

```
services:
  backend:
    image: python:3.12-slim

  database:
    image: postgres:16

  web:
    image: nginx:1.27
```

`tag` نسخه یا نوع image را مشخص می‌کند. مثلاً این‌ها با هم فرق دارند:

```
image: python:3.12
image: python:3.12-slim
image: python:3.12-alpine
```
---
## حالا اگر تگ نذاریم چی
اگر tag نگذاری:

```
image: nginx
```

عملاً Docker آن را این‌طور در نظر می‌گیرد:

```
image: nginx:latest
```
---
### برایه خلاصه
ا-`image` مشخص می‌کند container سرویس از چه image و چه tagای ساخته شود. اگر image در سیستم موجود نباشد، Docker معمولاً آن را از registry دریافت می‌کند. `build` برای ساخت image از Dockerfile خود پروژه است.
