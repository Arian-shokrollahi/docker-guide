# build context 
---
#### یکی از قسمت هایه مهمی که باید بهش توجه بکنید درون build کردن همین مفهمون build context 
#### خیلی کوتاه درمورد بیلد کانتکست
ا-<mark>ا-`build context` محدوده فایل‌هایی روی Host است که Docker در زمان **ساخت Image** اجازه دارد ببیند و از آن‌ها استفاده کند.</mark> 

---
##### بیلد کانتکست یعنی چی ؟
ا-`build context` یعنی **مسیر روی host که Docker اجازه دارد فایل‌های داخل آن را برای build ببیند و استفاده کند**.
مثلاً:

```
services:
  backend:
    build:
      context: ./backend
```

اگر ساختار این باشد:

```
project/
├── compose.yaml
└── backend/
    ├── Dockerfile
    ├── app.py
    └── requirements.txt
```

اینجا:

```
./backend
```

می‌شود **build context**.

یعنی Docker هنگام build می‌تواند از فایل‌های داخل `backend` استفاده کند، مثلاً:

```
COPY app.py /app/
COPY requirements.txt /app/
```

ولی معمولاً نمی‌تواند از بیرون context فایل بردارد:

```
COPY ../secret.txt /app/
```

چون `secret.txt` بیرون از `build context` است.

---
#### خیلی خلاصه بیلد کانتکست چیه

ا-Build Context = پوشه‌ای روی Host که فایل‌های موردنیاز برای ساخت Image داخل آن قرار دارند و Docker در زمان build به آن‌ها دسترسی دارد.
