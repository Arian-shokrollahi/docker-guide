# بریم سراغه کلید command 
---
## اول کلید command چیست ؟
ا-`command` یک **Service-level key** در Docker Compose است که برای تعیین یا **override کردن دستور اصلی اجرای کانتینر** استفاده می‌شود.

---
## حالا اگر در داکر فایل اون image ما CMD رو داشته باشیم که معادل همین command است اگر دوباره command  در کامپوس  تعریف کنیم کدوم اجرا میشه :
اگر داخل Dockerfile `CMD` داشته باشی و بعد در Compose هم `command` تعریف کنی، **`command` داخل Compose اجرا می‌شود و `CMD` ایمیج را override می‌کند.**

مثلاً Dockerfile:

```
CMD ["python", "app.py"]
```

Compose:

```
services:
  app:
    image: my-app
    command: python worker.py
```

در نهایت اجرا می‌شود:

```
python worker.py
```

نه:

```
python app.py
```

پس برای جزوه:

ا-**`command` در Docker Compose اولویت بالاتری از `CMD` داخل Dockerfile دارد و آن را جایگزین می‌کند.**

فقط اگر Image `ENTRYPOINT` هم داشته باشد، `command` معمولاً آرگومان‌های `ENTRYPOINT` را عوض می‌کند، نه خود `ENTRYPOINT` را.

---
## بریم سراغه مثال هایه واقعی
چند مثال واقعی از `command`:

```
services:
  backend:
    image: my-backend
    command: gunicorn app:app -b 0.0.0.0:5000
```

اینجا به‌جای CMD پیش‌فرض Image، backend با `gunicorn` اجرا می‌شود.

```
services:
  worker:
    image: my-backend
    command: celery -A app.celery worker --loglevel=info
```

اینجا از همان Image برنامه استفاده کردی، ولی این سرویس به‌جای API، نقش worker را اجرا می‌کند.

```
services:
  migrate:
    image: my-backend
    command: python manage.py migrate
```

اینجا سرویس فقط برای اجرای migration دیتابیس بالا می‌آید و بعد از تمام شدن کار خارج می‌شود.

```
services:
  frontend:
    image: node:20
    command: npm run dev
```

در محیط development می‌توانی به‌جای command پیش‌فرض Image، dev server را اجرا کنی.

```
services:
  debug:
    image: alpine
    command: sleep infinity
```

این خیلی کاربردیه؛ کانتینر را زنده نگه می‌داری تا بتوانی واردش شوی و debug کنی.

مثلاً:

```
docker compose exec debug sh
```

یک مثال حرفه‌ای‌تر:

```
services:
  backend:
    image: my-backend
    command: >
      sh -c "
      python manage.py migrate &&
      gunicorn app:app -b 0.0.0.0:8000
      "
```

اینجا اول migration اجرا می‌شود و اگر موفق بود، بعد backend بالا می‌آید.

پس کاربردهای واقعی `command` معمولاً این‌هاست: تغییر command اصلی Image، اجرای worker، migration، dev server، debug container و اجرای چند دستور پشت سر هم.
