### ساخت داکر فایل لول ۳ هنوز ساده و قابل کنترل
---
 
###  هدف نهایی این پروژه چیست؟

هدف نهایی اینه که یک برنامه Python داشته باشیم که مقدار یک `ENV` رو بخونه و داخل خروجی چاپ کنه.

در آخر می‌خوای وقتی container اجرا شد، اینو ببینی:

```
Environment: production
```

ساختار پروژه:

```
docker-python-env/
├── Dockerfile
└── app.py
```

داخل `app.py`:

```
import os

env = os.getenv("APP_ENV", "development")

print(f"Environment: {env}")

```

وظیفه تو اینه Dockerfile بنویسی که:

۱-از `python:3.12-slim` استفاده کنه  
۲-`WORKDIR` رو روی `/app` بذاره  
۳-فایل `app.py` رو داخل image کپی کنه  
۴-یک environment variable به اسم `APP_ENV` با مقدار `production` تعریف کنه  
۵-موقع اجرای container فایل `app.py` رو اجرا کنه

باید از این Instructionها استفاده کنی:

```
FROM
WORKDIR
COPY
ENV
CMD
```

در آخر باید با این:

```
docker build -t python-env-test .
```

و بعد:

```
docker run --rm python-env-test
```

این خروجی رو بگیری:

```
Environment: production
```

----
### بریم تو کارش سریع بگید چی میخوایم الان:
- ۱-ساخت پوشه پروژه
- ۲-ساخت داکر فایل 
- ۳-ساخت یه برنامه ساده
- ۴-ساخت image از رویه داکر فایل
- ۵-بعد اجرایه کانتینر از رویه اون image که از رویه docker file ساختیم و سپس چون قراره داخل اون فایل برنامه سادمون print شه اون متن و در داکر فایل در خط اخر با پایتون قراره اون فایل برنامه ساده توسط پایتون اجرا شه در صورت اینکه درست رفته باشیم جلو برایه ما اون پیغام چاپ میشه
---
#### ساخت پوشه پروژه :
‍
```
1-create project folder-->mkdir docker-python-env
2-touch Dockerfile
3-touch app.py
---
tree
---

docker-python-env/
├── Dockerfile
└── app.py

```

----
### اضافه کردن محتوا به فایل هایه پروژه
- ۱-به فایل برنامه سادمون 
```bash
import os

env = os.getenv("APP_ENV", "development")

print(f"Environment: {env}")

```
- ۲-نوشتن داکر فایل
```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY app.py /app
ENV APP_ENV="production"
CMD ["python", "app.py"]
```
---
### ساختن image از رویه dockerfile
- ساختن ایمیج با دستور docker build
```bash
docker build -t python-env-test .
```

---
### حالا اجرا کردن کانتینر
- چون میخوایم فقط اون خروجی هنگام ساخت کانتینر رو ببنیم فلگ  rm-- میزنیم که بعد اجرا پاک شه
```
docker run --rm python-env-test:latest
---
output:
---
Environment: production
root@alfamachine:~/docker-python-env# 

```
- تمام شد و خروجی همونطوری شد که میخواستیم

