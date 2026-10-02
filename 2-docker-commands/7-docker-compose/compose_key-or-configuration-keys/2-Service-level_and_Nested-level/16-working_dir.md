# working_dir
---
ا-`working_dir` یک **Service-level key** در Docker Compose است که مشخص می‌کند دستورهای داخل کانتینر از کدام مسیر اجرا شوند.

مثلاً:

```
services:
  app:
    image: python:3.12
    working_dir: /app
    command: python main.py
```

اینجا قبل از اجرای:

```
python main.py
```

دایرکتوری کاری کانتینر می‌شود:

```
/app
```

یعنی انگار داخل کانتینر این اتفاق افتاده:

```
cd /app
python main.py
```

اگر Dockerfile از قبل این را داشته باشد:

```
WORKDIR /app
```

و در Compose بنویسی:

```
working_dir: /src
```

مقدار Compose عملاً working directory زمان اجرا را تغییر می‌دهد.

برای جزوه:

ا-**`working_dir` مشخص می‌کند command یا process سرویس از داخل چه مسیری در کانتینر اجرا شود.**

خیلی کاربردیه وقتی فایل‌های پروژه در یک مسیر مشخص مثل `/app`، `/usr/src/app` یا `/workspace` قرار دارند.

---
## البته این رو توجه کن که باید مسیر کاری رو درست انتخاب کنی

ا- `working_dir` در Compose می‌تواند مسیر کاری پیش‌فرض Image را **override** کند و اگر اشتباه انتخابش کنی، برنامه‌ات ممکنه درست اجرا نشه.

مثلاً Dockerfile:

```
WORKDIR /app
COPY . /app
CMD ["python", "main.py"]
```

اگر Compose این‌طوری باشه:

```
services:
  app:
    image: my-app
    working_dir: /src
```

حالا command از `/src` اجرا می‌شود، نه `/app`.

اگر `main.py` فقط داخل `/app` باشد، این ممکنه fail شود:

```
python: can't open file 'main.py'
```

پس قانون ساده:

ا-**`WORKDIR` داخل Image = مسیر کاری پیش‌فرض**

ا-**`working_dir` داخل Compose = مسیر کاری زمان اجرا و دارای اولویت**

اگر در Compose `working_dir` نذاری، همان `WORKDIR` داخل Image استفاده می‌شود.
