# compose key entrypoint
---
ا-`entrypoint` یک **Service-level key** در Docker Compose است که مشخص می‌کند **برنامه‌ی اصلی کانتینر با چه executable یا scriptی شروع شود**.

فرقش با `command` اینه که معمولاً:

- ا-`entrypoint` = برنامه‌ی اصلی
- ا-`command` = آرگومان‌ها یا دستور پیش‌فرض آن برنامه

مثلاً اگر در Dockerfile داشته باشی:

```
ENTRYPOINT ["python"]
CMD ["app.py"]
```

یعنی در حالت عادی اجرا می‌شود:

```
python app.py
```

حالا اگر در Compose بنویسی:

```
services:
  app:
    image: my-app
    entrypoint: ["python3"]
```

ا-`ENTRYPOINT` اصلی Image override می‌شود و به‌جای `python` از `python3` استفاده می‌شود.

یا مثلاً:

```
services:
  app:
    image: my-app
    entrypoint: ["/app/start.sh"]
```

یعنی کانتینر با این script شروع شود.

رابطه‌ی مهمش با `command`:

```
services:
  app:
    image: my-app
    entrypoint: ["python"]
    command: ["worker.py"]
```

در نهایت اجرا می‌شود:

```
python worker.py
```

پس برای جزوه:

ا-**`entrypoint` برنامه یا script اصلی شروع کانتینر را تعیین می‌کند و `command` معمولاً آرگومان یا دستور پیش‌فرضی است که به آن داده می‌شود.**

و اگر در Compose `entrypoint` تعریف کنی، `ENTRYPOINT` موجود داخل Image را override می‌کند.
