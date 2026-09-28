### نوشتن داکر فایل سناریو دار لول ۲ هنوز ساده برایه این که درک کنید منطق دستورالعمل هایه داکر فایل رو و مسیر ایمیج ساختن تا اجرا کانتینر
---

بریم یک مرحله سخت‌تر، ولی هنوز کنترل‌شده و قابل فهم.

هدف نهایی این سناریو اینه که یک **Flask application واقعی** رو داخل Docker اجرا کنیم؛ یعنی در آخر وقتی Container بالا اومد، با مرورگر بری روی:

```
http://localhost:5000
```

و این پیام رو ببینی:

```
Hello from Docker Flask App
```

ساختار پروژه:

```
flask-docker-app/
├── Dockerfile
├── app.py
└── requirements.txt
```

داخل `requirements.txt`:

```
flask
```

داخل `app.py`:

```
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from Docker Flask App"

app.run(host="0.0.0.0", port=5000)

```
"
حالا وظیفه تو اینه Dockerfile رو خودت بنویسی.

باید از این Instructionها استفاده کنی:

```
FROM
WORKDIR
COPY
RUN
EXPOSE
CMD
```

چیزی که در آخر می‌خوایم بهش برسیم:

```
Dockerfile
   ↓
docker build
   ↓
Flask Image
   ↓
docker run -p 5000:5000
   ↓
Flask Container
   ↓
Browser
   ↓
Hello from Docker Flask App
```

این Dockerfile رو خودت بنویس و بفرست؛ من فقط بررسی می‌کنم و اگر جایی ایراد داشت، دقیقاً همون قسمت رو توضیح می‌دم.

---
### کد هایه سناریو 
- اول پوشه پروژه درست کردن پوشه ای به اسمه flast-docker-app
- دوم درست کردن فایل اپلیکیشن --->app.py وقرار دادن این محتوا درونش
```
in app.py
touch app.py -->cat > app.py
---
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from Docker Flask App"

app.run(host="0.0.0.0", port=5000)
```
- سوم فایل نیازمندی ها -->touch requirements.txt
- چهارم ساخت داکر فایل و قرار دادن این محتوا درونش
```dockerfile
FROM python:3.12
WORKDIR /app
COPY requirements.txt /app
RUN pip install -r requirements.txt
COPY app.py
EXPOSE 5000
CMD ["python", "app.py"]
```
#### توضیح قسمت هایه مختلف داکر فایل
۱-`FROM` برای مشخص کردن **Base Image** استفاده می‌شود.  
۲-`python:3.12` یعنی Image پایه ما Python نسخه ۳.۱۲ باشد.  
۳-`WORKDIR /app` مسیر کاری داخل Image را روی `/app` قرار می‌دهد.  
۴-`COPY requirements.txt /app` فایل `requirements.txt` را از سیستم Host داخل `/app` در Image کپی می‌کند.  
۵-`RUN pip install -r requirements.txt` موقع Build، پکیج‌های داخل `requirements.txt` را نصب می‌کند.  
۶-`COPY app.py .` فایل `app.py` را داخل مسیر کاری فعلی، یعنی `/app`، کپی می‌کند.  
۷-`EXPOSE 5000` مشخص می‌کند برنامه داخل Container روی پورت `۵۰۰۰` کار می‌کند.  
۸-`CMD ["python", "app.py"]` دستور پیش‌فرض زمان اجرای Container است و `app.py` را با Python اجرا می‌کند.

---
### و برایه تست اینکه درست اومده بالا و اینکه به ما اون متن داخله app.py رو نشون میده یا نه
```
# curl localhost:5000
Hello from Docker Flask Approot@alfamachine:~/flast-docker-app# 

```
خب همونطور که مشاهده میکنید درست کار میکند یا یک کار دیگه میتونید برید در مرورگر و بزنید localhost:5000 اینم میشه