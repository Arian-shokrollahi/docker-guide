

### ا-Multiple Mount چیست؟

ا-`Multiple Mounts` یعنی یک Container به‌صورت همزمان چند Mount Point مختلف داشته باشد.

دلیل استفاده این است که همه داده‌های داخل یک Container یک نوع نیستند. بعضی داده‌ها باید دائمی بمانند، بعضی باید از Host مدیریت شوند و بعضی کاملاً موقتی هستند.

مثلاً:

```
/app/config  → Bind Mount
/app/data    → Volume
/app/cache   → tmpfs
```

در پروژه‌های واقعی Multiple Mount باعث می‌شود هر بخش از Container را با Storage مناسب خودش مدیریت کنیم. این کار مدیریت Data، امنیت، Performance و نگهداری پروژه را بهتر می‌کند.

### چرا از Multiple Mount استفاده می‌کنیم؟

فرض کن یک Backend داری:

```
Backend Container

/app/config
→ Bind Mount
→ فایل config را از Host مدیریت می‌کنیم

/app/uploads
→ Volume
→ فایل‌های کاربران باید باقی بمانند

/app/cache
→ tmpfs
→ اطلاعات موقت هستند و لازم نیست روی Disk بمانند
```

اگر برای همه این قسمت‌ها فقط یک نوع Mount استفاده کنیم، طراحی درستی نداریم.

قاعده ساده:

```
Host-managed data  → Bind Mount
Persistent data    → Volume
Temporary data     → tmpfs
```

---

## روش‌های ساخت Multiple Mount

### 1. با `docker run` و `--mount`

این روش واضح‌ترین حالت است:

```
docker run -d \
  --name myapp \
  --mount type=bind,source=/home/user/config,target=/app/config,readonly \
  --mount type=volume,source=app-data,target=/app/data \
  --mount type=tmpfs,destination=/app/cache \
  myimage
```

اینجا یک Container سه Mount دارد:

```
/app/config → bind
/app/data   → volume
/app/cache  → tmpfs
```

---

### 2. با `docker run` و `-v`

برای `bind` و `volume` می‌توانیم چند بار `-v` استفاده کنیم:

```
docker run -d \
  --name myapp \
  -v /home/user/config:/app/config:ro \
  -v app-data:/app/data \
  --tmpfs /app/cache \
  myimage
```

نتیجه تقریباً همان است.

---

### 3. با Docker Compose

در پروژه واقعی معمولاً این روش خیلی پرکاربردتر است:

```
services:
  app:
    image: myapp

    volumes:
      - ./config:/app/config:ro
      - app-data:/app/data

    tmpfs:
      - /app/cache

volumes:
  app-data:
```

اینجا:

```
./config     → Bind Mount
app-data     → Volume
/app/cache   → tmpfs
```

---

### 4. Compose با Long Syntax

ا-Compose مدل کامل‌تر هم دارد:

```
services:
  app:
    image: myapp

    volumes:
      - type: bind
        source: ./config
        target: /app/config
        read_only: true

      - type: volume
        source: app-data
        target: /app/data

      - type: tmpfs
        target: /app/cache

volumes:
  app-data:
```

این مدل برای پروژه‌های بزرگ خواناتر است.

---

### یک مثال واقعی با Nginx

```
Nginx Container
│
├── /etc/nginx/nginx.conf
│     → Bind Mount
│
├── /usr/share/nginx/html
│     → Volume
│
└── /var/cache/nginx
      → tmpfs
```

Compose:

```
services:
  nginx:
    image: nginx:latest

    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - nginx-html:/usr/share/nginx/html

    tmpfs:
      - /var/cache/nginx

volumes:
  nginx-html:
```

### نکته مهم

تعداد Mountها محدود به یکی نیست. می‌توانی چندین Mount داشته باشی:

```
Container
├── Mount 1
├── Mount 2
├── Mount 3
├── Mount 4
└── ...
```

فقط معمولاً نباید دو Mount را روی **یک Target یکسان** تعریف کنی.

مثلاً این طراحی مشکل‌دار است:

```
/app → Volume
/app → Bind Mount
```

ولی این کاملاً درست است:

```
/app/config → Bind
/app/data   → Volume
/app/cache  → tmpfs
```

----
### ا-<mark> این از همه این قسمت مهمتره که ما میتونیم به چند روش کانتینر و سرویس بالا بیاریم </mark> 
عملاً دو حالت اصلی داری برای اینکه به یک Container چند Mount بدی:

1. موقع ساخت/اجرای Container با `docker run`

```
docker run -d \
  --name myapp \
  --mount type=bind,source=/home/user/config,target=/app/config \
  --mount type=volume,source=app-data,target=/app/data \
  --mount type=tmpfs,destination=/app/cache \
  myimage
```

2. داخل `docker compose`

```
services:
  app:
    image: myimage
    volumes:
      - ./config:/app/config
      - app-data:/app/data

    tmpfs:
      - /app/cache

volumes:
  app-data:
```

### ا-<MARK> حالا برایه هر کدوم از این مدل ها هم دو مدل داریم</MARK>

ساختار کلی Multiple Mount این شکلیه:

```
Multiple Mount
│
├── 1) docker run
│   ├── -v
│   └── --mount
│
└── 2) docker compose
    ├── Short Syntax
    └── Long Syntax
```

### 1. در `docker run`

با `-v`:

```
docker run -d \
  -v ./config:/app/config \
  -v app-data:/app/data \
  --tmpfs /app/cache \
  myimage
```

یا با `--mount`:

```
docker run -d \
  --mount type=bind,source=./config,target=/app/config \
  --mount type=volume,source=app-data,target=/app/data \
  --mount type=tmpfs,destination=/app/cache \
  myimage
```

### 2. در `docker compose`

Short Syntax:

```
services:
  app:
    image: myimage
    volumes:
      - ./config:/app/config
      - app-data:/app/data
    tmpfs:
      - /app/cache

volumes:
  app-data:
```

Long Syntax:

```
services:
  app:
    image: myimage

    volumes:
      - type: bind
        source: ./config
        target: /app/config

      - type: volume
        source: app-data
        target: /app/data

      - type: tmpfs
        target: /app/cache

volumes:
  app-data:
```

پس برای جزوه‌ات می‌تونی خیلی خلاصه بنویسی:

```
Multiple Mount را معمولاً به دو روش تعریف می‌کنیم:

1. docker run
   - -v
   - --mount

2. docker compose
   - Short Syntax
   - Long Syntax
```

فقط یک نکته: در `docker run`، برای `tmpfs` علاوه بر `--mount` دستور اختصاصی `--tmpfs` هم داریم
-  دستور اختصاصی `tmpfs` در `docker run` اینه:

```
docker run -d \
  --name myapp \
  --tmpfs /app/cache \
  myimage
```

یعنی مسیر:

```
/app/cache
```

داخل RAM به‌صورت `tmpfs` ساخته می‌شود.

می‌تونی option هم بدی، مثلاً محدودیت حجم:

```
docker run -d \
  --name myapp \
  --tmpfs /app/cache:rw,size=100m \
  myimage
```

پس برای `tmpfs` در `docker run` دو مدل اصلی داری:

```
1) --tmpfs
2) --mount type=tmpfs
```

مثلاً معادل هم:

```
--tmpfs /app/cache
```

و:

```
--mount type=tmpfs,destination=/app/cache
```

برای جزوه‌ات این ساختار دقیق‌تره:

```
docker run
├── -v
├── --mount
└── --tmpfs   ← مخصوص tmpfs
```
