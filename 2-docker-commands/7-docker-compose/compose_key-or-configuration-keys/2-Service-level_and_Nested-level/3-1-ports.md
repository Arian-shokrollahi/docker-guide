## بریم سراغه یه کلید مهم دیگه به اسمه ports
---
### مقدمه `ports`

کلید `ports` در Docker Compose برای اینه که یک پورت داخل container رو روی Host منتشر کنی تا از بیرون Docker بتونی به اون سرویس دسترسی داشته باشی.

مثلاً اگر Nginx داخل container روی پورت `80` کار می‌کنه، با `ports` می‌تونی کاری کنی از روی سیستم خودت با `localhost:8080` بهش وصل بشی.

ساختار اصلی:

```
ports:
  - "HOST_PORT:CONTAINER_PORT"
```

یعنی همیشه:

```
سمت چپ  = Host
سمت راست = Container
```

مثال:

```
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
```

تحلیل این خط:

```
8080 : 80
 │      │
 │      └── پورت داخل Container
 │
 └───────── پورت روی Host
```

پس وقتی در مرورگر بزنی:

```
http://localhost:8080
```

مسیر درخواست میشه:

```
Browser
   ↓
Host port 8080
   ↓
Docker
   ↓
Container port 80
   ↓
Nginx
```

### یک مثال واقعی

فرض کن Backend تو داخل container روی پورت `5000` اجرا میشه:

```
services:
  backend:
    image: my-backend:v1
    ports:
      - "8000:5000"
```

اینجا برنامه داخل container هنوز روی:

```
5000
```

کار می‌کنه.

ولی از روی سیستم خودت باید بری به:

```
localhost:8000
```

یعنی:

```
HOST            CONTAINER
8000     →      5000
```

پس تغییر `8000` به `9000` هیچ تغییری در برنامه داخل container ایجاد نمی‌کنه:

```
ports:
  - "9000:5000"
```

حالا فقط آدرس بیرونی میشه:

```
localhost:9000
```

ولی برنامه همچنان داخل container روی `5000` است.

---

### تفاوت `EXPOSE` و `ports`

در Dockerfile ممکنه داشته باشی:

```
EXPOSE 5000
```

این به معنی publish کردن پورت روی Host نیست.

`EXPOSE` فقط مشخص می‌کنه که این image یا application **قرار است روی چه پورتی داخل container کار کند**.

ولی:

```
ports:
  - "8000:5000"
```

واقعاً ارتباط Host با container رو برقرار می‌کنه.

پس برای یادداشت:

```
EXPOSE
↓
مشخص می‌کند برنامه داخل Container
روی چه پورتی کار می‌کند.
Host access ایجاد نمی‌کند.


ports
↓
پورت Container را روی Host
Publish می‌کند.
```

مثلاً:

```
EXPOSE 5000
```

و:

```
ports:
  - "8000:5000"
```

با هم یعنی:

```
Application
   ↓
Container :5000
   ↓
Published by ports
   ↓
Host :8000
   ↓
localhost:8000
```

قانون ساده‌ای که حفظ کنی:

```
HOST:CONTAINER

8080:80
8000:5000
3000:3000
```

**همیشه چپ Host، راست Container.**


