## دستور `docker logs` چیست؟

```bash
docker logs
```

برای **دیدن خروجی و لاگ‌های یک Container در حال اجرا یا متوقف‌شده** استفاده می‌شود.

به زبان ساده:

> هر چیزی که برنامه داخل Container روی Terminal چاپ کند (stdout و stderr)، Docker ذخیره می‌کند و با `docker logs` می‌توانی ببینی.

---

## ساختار دستور:

```bash
docker logs [OPTIONS] CONTAINER
```

مثال:

```bash
docker logs nginx-container
```

---

# مثال واقعی

یک Container اجرا می‌کنیم:

```bash
docker run -d --name web nginx
```

حالا:

```bash
docker logs web
```

ممکن است خروجی بدهد:

```text
/docker-entrypoint.sh: Configuration complete
nginx: [notice] start worker processes
```

یعنی Nginx چه اتفاقاتی را گزارش کرده.

---

# مهم‌ترین Optionهای `docker logs`

## 1) دنبال کردن لحظه‌ای Logها (`-f`)

```bash
docker logs -f web
```

`-f` یعنی:

**follow**

یعنی مثل:

```bash
tail -f
```

هر Log جدیدی که تولید شود همان لحظه نشان می‌دهد.

کاربرد:

وقتی برنامه را تست می‌کنی:

```text
Request آمد
      ↓
Error ایجاد شد
      ↓
همان لحظه در Terminal می‌بینی
```

---

## 2) دیدن آخرین چند خط

```bash
docker logs --tail 50 web
```

فقط ۵۰ خط آخر را نشان می‌دهد.

برای Containerهایی که هزاران خط Log دارند خیلی کاربردی است.

---

## 3) دیدن Log از یک زمان خاص

```bash
docker logs --since 10m web
```

یعنی:

لاگ‌های ۱۰ دقیقه اخیر را نشان بده.

مثال:

```bash
docker logs --since 1h web
```

یک ساعت اخیر.

---

## 4) نمایش زمان Logها

```bash
docker logs -t web
```

خروجی:

```text
2026-09-25T10:30:20 nginx started
```

---

# چگونه با `docker logs` Debug کنیم؟

فرض کن یک برنامه بالا نمی‌آید.

## مرحله ۱: وضعیت Container

```bash
docker ps -a
```

می‌بینی:

```text
STATUS
Exited (1)
```

یعنی برنامه Crash کرده.

---

## مرحله ۲: دیدن دلیل خطا

```bash
docker logs container_name
```

مثلاً:

```text
ERROR: database connection failed
```

می‌فهمی مشکل از Database است.

---

# چند سناریوی واقعی Debug

## سناریو ۱: برنامه Crash می‌کند

Container:

```bash
docker ps -a
```

می‌بینی:

```text
Exited (1)
```

بعد:

```bash
docker logs app
```

خروجی:

```text
Error: Cannot connect to MongoDB
```

نتیجه:

مشکل از Connection دیتابیس است، نه Docker.

---

## سناریو ۲: Nginx جواب نمی‌دهد

می‌زنی:

```bash
docker logs nginx
```

مثلاً:

```text
nginx: [emerg] invalid configuration
```

می‌فهمی مشکل از فایل Config است.

---

## سناریو ۳: بررسی درخواست‌های ورودی

برای بعضی سرویس‌ها:

```bash
docker logs -f web
```

بعد درخواست می‌فرستی:

```bash
curl localhost:8080
```

و Log را می‌بینی:

```text
GET /index.html 200
```

---

# تفاوت `docker logs` و `docker inspect`

خیلی مهم:

### docker logs:

می‌پرسد:

> برنامه داخل Container چه چیزی گزارش داده؟

مثلاً:

- Error برنامه
    
- Crash
    
- Request
    
- Warning
    

---

### docker inspect:

می‌پرسد:

> Docker این Container را چگونه ساخته؟

مثلاً:

- Network
    
- IP
    
- Volume
    
- Environment
    
- Port
    

---

Workflow واقعی Debug:

```text
مشکل داریم
     |
     ↓
docker ps -a
     |
     ↓
docker logs container
     |
     ↓
خطای برنامه را پیدا کن
     |
     ↓
docker inspect container
     |
     ↓
تنظیمات Docker را بررسی کن
```

---

خلاصه:

```bash
docker logs
```

= **دیدن اتفاقاتی که داخل Container افتاده**

و در DevOps یکی از اولین دستورهایی است که وقتی یک Container مشکل دارد اجرا می‌کنی.