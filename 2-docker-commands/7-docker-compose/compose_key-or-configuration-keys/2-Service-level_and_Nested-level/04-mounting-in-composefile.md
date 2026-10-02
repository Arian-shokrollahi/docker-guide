# بریم سراغه mounting in compose file
---
## درمورد volume خیلی توضیح دادیم:
- ۱- هم در فولدر volume
- ۲-هم در فولد قبلی که درمورد top level keys توضیح دادم در بخش volume
- ۳-در این قسمت هم دوباره بهتون توضیح میدم
---
 برای mount کردن داخل Docker Compose سه مدل اصلی داریم:

1. `bind`
2. `volume`
3. `tmpfs`

و برای `bind` و `volume` هم می‌توانی **short syntax** یا **long syntax** بنویسی. برای `tmpfs` هم هم حالت ساده داریم هم long syntax.

### 1) Bind Mount

برای وقتی که می‌خواهی یک **پوشه یا فایل واقعی روی Host** را مستقیم به container وصل کنی.

کاربرد رایج: توسعه، سورس‌کد، config.

#### Short syntax

```
services:
  backend:
    volumes:
      - ./src:/app/src
```

ساختار:

```
HOST_PATH : CONTAINER_PATH
```

مثلاً:

```
./src      → /app/src
Host          Container
```

می‌توانی `ro` یا `rw` هم بدهی:

```
volumes:
  - ./config:/app/config:ro
```

#### Long syntax

```
services:
  backend:
    volumes:
      - type: bind
        source: ./src
        target: /app/src
```

یعنی:

```
type   = bind
source = مسیر Host
target = مسیر Container
```

---

### 2) Named Volume

برای ذخیره اطلاعاتی که باید بعد از حذف یا recreate شدن container باقی بمانند.

کاربرد خیلی رایج: دیتابیس.

مثلاً PostgreSQL:

#### Short syntax

```
services:
  db:
    image: postgres:16
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data:
```

ساختار:

```
VOLUME_NAME : CONTAINER_PATH
```

اینجا:

```
postgres-data
      ↓
Docker-managed storage

/var/lib/postgresql/data
      ↓
مسیر داخل Container
```

#### Long syntax

```
services:
  db:
    image: postgres:16
    volumes:
      - type: volume
        source: postgres-data
        target: /var/lib/postgresql/data

volumes:
  postgres-data:
```

یعنی:

```
type   = volume
source = نام Volume
target = مسیر Container
```

---

### 3) tmpfs

`tmpfs` اطلاعات را روی RAM نگه می‌دارد، نه روی دیسک.

وقتی container متوقف یا حذف شود، اطلاعات tmpfs از بین می‌روند.

مناسب برای:

```
cache
temporary files
session data
فایل‌های موقت حساس
```

مثلاً:

```
/app/cache
```

#### حالت ساده

```
services:
  backend:
    image: my-api
    tmpfs:
      - /app/cache
```

یعنی:

```
RAM
 ↓
/app/cache داخل Container
```

می‌توانی size هم مشخص کنی:

```
services:
  backend:
    tmpfs:
      - /app/cache:size=256m
```

#### Long syntax

می‌توانی از `volumes` با `type: tmpfs` هم استفاده کنی:

```
services:
  backend:
    volumes:
      - type: tmpfs
        target: /app/cache
        tmpfs:
          size: 268435456
```

اینجا `268435456` بایت تقریباً برابر `256MB` است.

---

### مقایسه خیلی مهم

|نوع|Source کجاست؟|اطلاعات بعد از حذف Container|
|---|---|---|
|`bind`|مسیر واقعی Host|باقی می‌ماند|
|`volume`|توسط Docker مدیریت می‌شود|باقی می‌ماند|
|`tmpfs`|RAM|از بین می‌رود|

و از نظر syntax:

```
# Bind - Short
- ./src:/app/src
```

```
# Bind - Long
- type: bind
  source: ./src
  target: /app/src
```

```
# Volume - Short
- db-data:/var/lib/postgresql/data
```

```
# Volume - Long
- type: volume
  source: db-data
  target: /var/lib/postgresql/data
```

```
# tmpfs - Simple
tmpfs:
  - /app/cache
```

```
# tmpfs - Long
volumes:
  - type: tmpfs
    target: /app/cache
```

برای حفظ کردن:

```
bind   → Host folder/file
volume → Docker storage
tmpfs  → RAM
```

و نکته مهم: **Bind و Volume معمولاً زیر `volumes:` سرویس می‌آیند؛ tmpfs هم می‌تواند با کلید `tmpfs:` نوشته شود یا با long syntax زیر `volumes:` بیاید.**

---
### cheetsheet
این جمع‌بندی رو آخر جزوه‌ات بذار:

### جمع‌بندی ساختار Mountها در Compose

```
1) Short Syntax
2) Long Syntax
```

#### 1) Short Syntax

```
services:
  app:
    volumes:
      # Bind Mount
      - ./src:/app/src

      # Named Volume
      - app-data:/app/data

    tmpfs:
      # tmpfs
      - /app/cache

volumes:
  app-data:
```

ساختار کلی:

```
Bind:
HOST_PATH:CONTAINER_PATH

Volume:
VOLUME_NAME:CONTAINER_PATH

tmpfs:
CONTAINER_PATH
```

---

#### 2) Long Syntax

```
services:
  app:
    volumes:

      # Bind Mount
      - type: bind
        source: ./src
        target: /app/src

      # Named Volume
      - type: volume
        source: app-data
        target: /app/data

      # tmpfs
      - type: tmpfs
        target: /app/cache

volumes:
  app-data:
```

ساختار کلی:

```
type:
source:
target:
```

ولی بسته به نوع Mount:

```
Bind
type: bind
source: مسیر Host
target: مسیر Container
```

```
Volume
type: volume
source: نام Volume
target: مسیر Container
```

```
tmpfs
type: tmpfs
target: مسیر Container
```

نکته مهم: `tmpfs` چون روی RAM ساخته می‌شود، معمولاً `source` ندارد.

### خلاصه حفظی

```
Short Syntax
Bind   → ./host:/container
Volume → volume-name:/container
tmpfs  → /container/path
```

```
Long Syntax
Bind   → type + source + target
Volume → type + source + target
tmpfs  → type + target
```

و اگر بخوای خیلی خلاصه‌تر حفظ کنی:

```
source = چیزی که Mount می‌شود
target = جایی که داخل Container دیده می‌شود
```

فقط `tmpfs` استثناء است چون `source` آن RAM است و معمولاً خودت مسیر source مشخص نمی‌کنی.
