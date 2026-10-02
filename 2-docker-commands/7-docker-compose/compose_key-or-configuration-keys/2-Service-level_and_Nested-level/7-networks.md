# بریم سراغه نتورک
---

### مقدمه `networks` در Service Level

کلید `networks` در سطح سرویس مشخص می‌کند که یک container عضو کدام Docker Networkها باشد.

یعنی باهاش تعیین می‌کنی:

> این سرویس با چه سرویس‌هایی بتواند از طریق شبکه داخلی Docker ارتباط داشته باشد.

مثلاً:

```
services:
  backend:
    image: my-api
    networks:
      - app-network
```

اینجا `backend` عضو `app-network` شده.

---

### ساختار

در سطح سرویس:

```
services:
  SERVICE_NAME:
    networks:
      - NETWORK_NAME
```

و خود Network معمولاً پایین Compose در سطح Top-level تعریف می‌شود:

```
networks:
  app-network:
```

پس:

```
services
└── backend
    └── networks     ← Service-level key

networks
└── app-network      ← Top-level definition
```

---

### مثال واقعی چندسرویسی

```
services:
  frontend:
    image: nginx
    networks:
      - frontend-network

  backend:
    image: my-api
    networks:
      - frontend-network
      - backend-network

  db:
    image: postgres:16
    networks:
      - backend-network

networks:
  frontend-network:
  backend-network:
```

تحلیل:

```
frontend
   │
   │ frontend-network
   │
backend
   │
   │ backend-network
   │
database
```

یعنی:

```
frontend ↔ backend
```

می‌تونن با هم ارتباط داشته باشن.

و:

```
backend ↔ db
```

هم می‌تونن با هم ارتباط داشته باشن.

ولی:

```
frontend ✖ db
```

مستقیم همدیگه رو نمی‌بینن چون Network مشترک ندارن.

این دقیقاً یکی از کاربردهای مهم چند Network در Productionه.

---

### ارتباط سرویس‌ها با اسم Service

اگر دو سرویس داخل Network مشترک باشن:

```
services:
  backend:
    networks:
      - app-net

  db:
    image: postgres:16
    networks:
      - app-net
```

ا-Backend می‌تونه با اسم سرویس `db` به دیتابیس وصل بشه:

```
db:5432
```

مثلاً:

```
environment:
  DB_HOST: db
  DB_PORT: "5432"
```

لازم نیست IP کانتینر رو بدونی.

ا-Docker DNS داخلی خودش:

```
db
↓
IP container
```

رو Resolve می‌کنه.

---

### حالت ساده و Long Syntax

مدل ساده:

```
services:
  backend:
    networks:
      - app-network
```

مدل کامل‌تر:

```
services:
  backend:
    networks:
      app-network:
        aliases:
          - api
```

حالا این سرویس داخل Network علاوه بر اسم اصلیش می‌تونه با:

```
api
```

هم پیدا بشه.

---

### نکته خیلی مهم

اگر اصلاً `networks` ننویسی، Docker Compose خودش یک Network پیش‌فرض برای پروژه می‌سازه و سرویس‌ها رو به اون وصل می‌کنه.

مثلاً:

```
services:
  backend:
    image: my-api

  db:
    image: postgres:16
```

Compose خودش چیزی شبیه این می‌سازه:

```
project_default
```

و هر دو سرویس رو عضو همون Network می‌کنه.

پس برای پروژه ساده، حتی بدون تعریف Network هم سرویس‌ها معمولاً می‌تونن با اسم همدیگه ارتباط بگیرن.

### جمع‌بندی برای جزوه

> ا-`networks` در Service Level مشخص می‌کند هر سرویس عضو کدام Docker Network باشد و در نتیجه با چه سرویس‌هایی بتواند ارتباط داخلی داشته باشد.

خلاصه:

```
Network مشترک
→ ارتباط مستقیم ممکن است

Network جدا
→ ارتباط مستقیم وجود ندارد
```

و مهم‌ترین کاربردش:

```
جداسازی
Frontend
Backend
Database
```

برای معماری تمیزتر و امن‌تر.
