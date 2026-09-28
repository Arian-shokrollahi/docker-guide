## داکر docker instruction چیست

ا-**Docker Instruction**: دستورهای مخصوص داخل `Dockerfile` هستند که به Docker می‌گویند هنگام ساخت Image چه کاری انجام دهد.

ساختار کلی:

```
INSTRUCTION argument
```

مثال:

```
FROM node:20
```

- `FROM` → Instruction (نوع دستور)
- `node:20` → Argument (مقداری که به دستور داده می‌شود)

---

در Dockerfile حدوداً ۱۵ تا Instruction اصلی داریم، ولی در کار واقعی همه به یک اندازه مهم نیستند. بعضی‌ها را هر روز استفاده می‌کنی، بعضی‌ها فقط در شرایط خاص.

من بر اساس اهمیت برای مسیر DevOps دسته‌بندی می‌کنم:

---

# 1) FROM ⭐⭐⭐⭐⭐ (خیلی مهم)

### کار:

مشخص می‌کند Image از چه چیزی ساخته شود.

مثال:

```
FROM node:20
```

یعنی:

"من یک محیط آماده Node.js نسخه 20 می‌خواهم."

یا:

```
FROM python:3.12
```

برای برنامه Python.

معمولاً اولین خط Dockerfile است.

مثال واقعی:

```
FROM nginx:alpine
```

برای سرو کردن سایت با nginx.

---

# 2) WORKDIR ⭐⭐⭐⭐⭐

### کار:

مسیر کاری داخل Container را مشخص می‌کند.

مثلاً:

```
WORKDIR /app
```

بعد از این:

```
COPY . .
```

یعنی فایل‌ها داخل:

```
/app
```

کپی می‌شوند.

به جای اینکه بنویسی:

```
cd /app
```

از WORKDIR استفاده می‌کنی.

تقریباً همیشه استفاده می‌شود.

---

# 3) COPY ⭐⭐⭐⭐⭐

### کار:

فایل‌های پروژه را از سیستم خودت وارد Image می‌کند.

مثال:

```
COPY package.json .
```

یا:

```
COPY . .
```

یعنی کل پروژه را داخل Image بیاور.

مثلاً:

قبل:

```
Laptop

my-app
 ├── server.js
 └── package.json
```

بعد از COPY:

```
Container

/app
 ├── server.js
 └── package.json
```

---

# 4) RUN ⭐⭐⭐⭐⭐

### کار:

دستورهایی را هنگام ساخت Image اجرا می‌کند.

مثلاً نصب پکیج:

```
RUN npm install
```

یا:

```
RUN apt update && apt install nginx
```

نکته مهم:

RUN در زمان:

```
docker build
```

اجرا می‌شود.

نه وقتی Container روشن می‌شود.

---

# 5) CMD ⭐⭐⭐⭐⭐

### کار:

مشخص می‌کند Container وقتی اجرا شد چه کاری انجام دهد.

مثال:

```
CMD ["node","server.js"]
```

وقتی:

```
docker run myapp
```

می‌زنی، این اجرا می‌شود.

---

فرق مهم:

### RUN:

زمان ساخت Image:

```
docker build
```

### CMD:

زمان اجرای Container:

```
docker run
```

---

# 6) ENTRYPOINT ⭐⭐⭐⭐

شبیه CMD است، ولی سخت‌تر قابل تغییر است.

مثال:

```
ENTRYPOINT ["nginx"]
```

یعنی این Container همیشه nginx اجرا می‌کند.

معمولاً در image های حرفه‌ای بیشتر دیده می‌شود.

---

# 7) ENV ⭐⭐⭐⭐

### کار:

متغیر محیطی تعریف می‌کند.

مثال:

```
ENV PORT=3000
```

داخل برنامه:

```
PORT
```

قابل استفاده است.

در پروژه واقعی زیاد استفاده می‌شود:

مثلاً:

```
DATABASE_URL
API_KEY
NODE_ENV
```

---

# 8) ARG ⭐⭐⭐

شبیه ENV است ولی برای زمان Build.

مثال:

```
ARG VERSION=1.0
```

زمان build:

```
docker build --build-arg VERSION=2.0 .
```

استفاده می‌شود.

---

# 9) EXPOSE ⭐⭐⭐

### کار:

اعلام می‌کند برنامه روی چه پورتی گوش می‌دهد.

مثال:

```
EXPOSE 8080
```

اما:

❌ پورت را باز نمی‌کند.

باز کردن پورت با:

```
docker run -p 8080:8080
```

است.

---

# 10) VOLUME ⭐⭐⭐

برای نگهداری اطلاعات دائمی.

مثلاً Database:

```
VOLUME /var/lib/mysql
```

اگر Container پاک شد، دیتا باقی می‌ماند.

---

# 11) USER ⭐⭐⭐

مشخص می‌کند Container با چه کاربری اجرا شود.

مثلاً:

```
USER node
```

برای امنیت مهم است.

در Production زیاد استفاده می‌شود.

---

# 12) LABEL ⭐⭐

برای اضافه کردن اطلاعات به Image:

```
LABEL maintainer="devops@example.com"
```

بیشتر برای مدیریت Image ها.

---

# 13) HEALTHCHECK ⭐⭐⭐

بررسی می‌کند سرویس سالم است یا نه.

مثال:

```
HEALTHCHECK CMD curl localhost:3000
```

در محیط Production کاربرد دارد.

---

# 14) ADD ⭐⭐

شبیه COPY است ولی قابلیت‌های اضافه دارد.

مثلاً:

```
ADD file.tar.gz /
```

امروزه بیشتر مواقع:

```
COPY
```

ترجیح داده می‌شود.

---

# 15) ONBUILD ⭐

برای ساخت Image های پایه است.

مثلاً کسی که Image برای دیگران منتشر می‌کند.

در کار روزمره کمتر استفاده می‌شود.

---

## اگر بخواهم برای سطح DevOps اولویت بدهم:

### باید خیلی خوب بلد باشی:

1. ⭐ FROM
2. ⭐ WORKDIR
3. ⭐ COPY
4. ⭐ RUN
5. ⭐ CMD
6. ⭐ ENTRYPOINT
7. ⭐ ENV
8. ⭐ EXPOSE

### بعد برو سراغ:

9. ARG
10. VOLUME
11. USER
12. HEALTHCHECK
13. Multi-stage build

با همین ۸ تای اول می‌توانی حدود ۸۰٪ Dockerfileهای واقعی شرکت‌ها را بخوانی و بنویسی.ش
