برای **وارد شدن به Shell داخل یک Container** در Docker معمولاً از دستور:

```bash
docker exec -it CONTAINER_NAME bash
```

یا:

```bash
docker exec -it CONTAINER_NAME sh
```

استفاده می‌کنیم.

---

## ا-`docker exec` چیست؟

یعنی:

> یک دستور جدید را داخل یک Container در حال اجرا اجرا کن.

ساختار:

```bash
docker exec [OPTIONS] CONTAINER COMMAND
```

مثال:

```bash
docker exec -it web bash
```

تجزیه:

```text
docker exec
      |
      ↓
اجرا کردن دستور داخل Container

-it
      |
      ↓
باز کردن ترمینال تعاملی

web
      |
      ↓
نام Container

bash
      |
      ↓
Shell که می‌خواهیم اجرا کنیم
```

---

## مثال واقعی

اول یک Container اجرا می‌کنیم:

```bash
docker run -d --name mynginx nginx
```

بررسی:

```bash
docker ps
```

خروجی:

```text
CONTAINER ID   IMAGE   NAMES
abc123         nginx   mynginx
```

حالا وارد Container می‌شویم:

```bash
docker exec -it mynginx bash
```

اگر موفق شود، Prompt تغییر می‌کند:

قبل:

```bash
user@server:~$
```

بعد:

```bash
root@abc123:/#
```

یعنی الان داخل Container هستی.

---

## داخل Container چه کارهایی می‌توانی بکنی؟

مثلاً:

دیدن فایل‌ها:

```bash
ls
```

دیدن مسیر:

```bash
pwd
```

دیدن Processها:

```bash
ps aux
```

بررسی فایل‌های برنامه:

```bash
cd /usr/share/nginx/html
ls
```

---

## تفاوت `bash` و `sh`

بعضی Imageها Bash ندارند.

مثلاً Alpine:

```bash
docker run -d --name alpine alpine sleep 1000
```

اگر بزنی:

```bash
docker exec -it alpine bash
```

خطا می‌دهد:

```text
bash: not found
```

چون Alpine فقط `sh` دارد.

پس:

```bash
docker exec -it alpine sh
```

درست است.

---

## تفاوت `docker exec` و `docker run -it`

این خیلی مهم است:

### ساخت Container جدید و ورود:

```bash
docker run -it ubuntu bash
```

یعنی:

- یک Container جدید بساز
    
- وارد Shell شو
    

---

### ورود به Container موجود:

```bash
docker exec -it ubuntu1 bash
```

یعنی:

- Container از قبل وجود دارد
    
- فقط واردش شو
    

---

Workflow معمول DevOps:

```text
docker run
     |
     ↓
Container اجرا می‌شود
     |
     ↓
docker ps
     |
     ↓
docker exec -it container bash
     |
     ↓
کار داخل Container
```

خلاصه:

ا-**`docker exec -it` درِ ورود به داخل یک Container در حال اجرا است.**
