### بریم سراغ `build`.

ا-`build` وقتی استفاده میشه که بخوای **image رو خودت از روی Dockerfile بسازی**.

ساده‌ترین حالت:

```
services:
  backend:
    build: .
```

یعنی:

> برو داخل همین مسیر `.` و دنبال Dockerfile بگرد و ازش image بساز.

ساختارش:

```
services
└── backend
    └── build
```

پس خود `build` یک **service-level key** هست.

حالت کامل‌تر:

```
services:
  backend:
    build:
      context: .
      dockerfile: Dockerfile
      args:
        APP_ENV: production
      target: runtime
```

مهم‌ترین nested keyهای داخل `build`:

|key|کاربرد|
|---|---|
|`context`|مسیر build context|
|`dockerfile`|اسم یا مسیر Dockerfile|
|`args`|مقدار دادن به `ARG` داخل Dockerfile|
|`target`|انتخاب stage مشخص در multi-stage build|
|`cache_from`|استفاده از cache قبلی|
|`cache_to`|ذخیره build cache|

مثلاً:

```
services:
  api:
    build:
      context: ./backend
      dockerfile: Dockerfile.prod
```

یعنی Docker فقط فایل‌های داخل:

```
./backend
```

رو به عنوان **build context** می‌بینه.

و Dockerfile مورد استفاده:

```
./backend/Dockerfile.prod
```

مثال `args`:

Dockerfile:

```
ARG APP_ENV
RUN echo $APP_ENV
```

Compose:

```
services:
  backend:
    build:
      context: .
      args:
        APP_ENV: production
```

اینجا:

```
Compose
   ↓
APP_ENV=production
   ↓
Docker build
   ↓
ARG APP_ENV
```

و `target` برای multi-stage خیلی مهمه:

```
FROM node:22 AS builder
RUN npm run build

FROM nginx AS production
COPY --from=builder /app/dist /usr/share/nginx/html
```

Compose:

```
services:
  frontend:
    build:
      context: .
      target: production
```

یعنی:

> تا stage به اسم `production` build کن.

برای یادگیری الان این ۴ تا رو خیلی خوب بلد باش:

```
build
├── context
├── dockerfile
├── args
└── target
```

و یک نکته خیلی مهم:

```
services:
  backend:
    build: .
    image: my-backend:v1
```

اینجا `build` میگه:

> image رو چطور بساز.

و `image` میگه:

> اسم image ساخته‌شده چی باشه.

یعنی:

```
build → HOW
image → NAME
```

اگر بخوای قدم بعدی رو بریم روی `context` چون از همه مهم‌تره و معمولاً خیلی‌ها همونجا اشتباه می‌کنن.
