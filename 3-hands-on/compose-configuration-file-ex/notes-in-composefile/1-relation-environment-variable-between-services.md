# docker compose architect
---
## درمورد چی قراره صحبت کنیم درون این قسمت 
- این قسمت درمورد معماری docker compose  قراره صحبت کنیم 
- این قسمت رو خوب درک کنید خیلی خوب میتونید قسمت هایی از جمله environment variable هارو متوجه شید و بتونید در نوشتن داکر کامپوس فایل از کلید ها و ارگومان ها استفاده کنید
---
##### در یک معماری رایج Docker Compose معمولاً مسیر ارتباط این‌طوریه:

```
User
  ↓
Frontend
  ↓
Backend
  ↓
Database
```

یعنی:

```
Frontend → Backend
Backend → Database
```

نکته مهم اینه که معمولاً:


ا-Frontend ❌ مستقیم به Database وصل نمی‌شود.


مثلاً اگر این سه سرویس را داشته باشیم:

```
services:
  frontend:
    ...

  backend:
    ...

  database:
    image: postgres:16
```

ارتباط منطقی‌شان می‌شود:

```
frontend
   │
   │ HTTP Request
   ▼
backend
   │
   │ SQL / Database Connection
   ▼
database
```

مثلاً Frontend می‌گوید:

```
GET /api/users
```

این درخواست می‌رود به Backend.

بعد Backend ممکن است برود از PostgreSQL اطلاعات بگیرد:

```
SELECT * FROM users;
```

و نتیجه را دوباره برگرداند:

```
Database → Backend → Frontend
```

پس تا اینجا پایه‌ای‌ترین نکته‌ای که باید در ذهنت قفل شود این است:

>ا- **Frontend مصرف‌کننده API بک‌اند است و Backend مصرف‌کننده Database است.**

حالا دقیقاً از همین نقطه می‌توانیم ریز شویم روی Environment Variableها و بررسی کنیم **برای اتصال Frontend به Backend چه متغیرهایی لازم داریم، بعد برای Backend به Database چه متغیرهایی لازم داریم.**

---
## یه چیز حالا شاید جالب براتون به ربط به docker compose بریم درمورد این بگیم که nignx چی رو مخفی میکنه در نقش reverse proxy
معمولاً **Nginx به‌عنوان Reverse Proxy بک‌اند را از دید مستقیم کاربر مخفی می‌کند**.

یعنی به‌جای اینکه کاربر مستقیم برود روی:

```
http://backend:5000
```

یا مثلاً:

```
http://server-ip:5000
```

کاربر فقط این را می‌بیند:

```
https://example.com
```

و Nginx درخواست را پشت صحنه می‌فرستد به Backend:

```
User
  ↓
Nginx
  ↓
Backend
  ↓
Database
```

پس در حالت معمول:

```
Nginx → Backend را پشت خودش پنهان می‌کند
```

و Database هم اصولاً نباید مستقیم در معرض کاربر باشد.

مثلاً:

```
location /api/ {
    proxy_pass http://backend:5000;
}
```

کاربر می‌زند:

```
example.com/api/users
```

ولی Nginx درخواست را می‌برد به:

```
backend:5000/users
```

در نتیجه کاربر لازم نیست بداند Backend روی چه IP، چه Container یا حتی چه پورتی اجرا می‌شود.
