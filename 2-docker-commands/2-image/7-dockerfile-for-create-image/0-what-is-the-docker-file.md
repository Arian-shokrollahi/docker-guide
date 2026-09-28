## داکر فایل چیست:
ا-**Dockerfile یک فایل متنی است که به Docker می‌گوید چطور یک Docker Image بسازد.**

یعنی داخلش مشخص می‌کنی:

- از چه سیستم پایه‌ای شروع کند (`FROM`)
- چه فایل‌هایی را داخل image کپی کند (`COPY`)
- چه پکیج‌هایی نصب کند (`RUN`)  
- برنامه از کجا اجرا شود (`CMD`)

مثلاً:

```
FROM python:3.12

WORKDIR /app

COPY app.py .

CMD ["python","app.py"]
```

بعد Docker می‌گوید:

"باشه، طبق این دستورها یک Image بساز."

با:

```
docker build -t my-app .
```

خروجی:

```
Dockerfile  --->  Image  --->  Container
```

پس خیلی خلاصه:

ا-**Dockerfile = نقشه ساخت Image**  
ا-**Image = بسته آماده اجرای برنامه**  
ا-**Container = اجرای آن Image**

مثلاً مثل ساخت ماشین:

- ا-Dockerfile = نقشه و دستور ساخت ماشین
- ا-Image = ماشین ساخته‌شده
- ا-Container = ماشینی که روشن شده و حرکت می‌کند

---

### چیارو در مسیر داکر فایل باید یادبگیری
---


برای اینکه بگویی **Dockerfile را یاد گرفتم** لازم نیست همه چیز را حفظ باشی. باید بتوانی یک application را از صفر به یک Image قابل اجرا تبدیل کنی.

این موارد را باید بلد باشی:

---

## 1) مفهوم Dockerfile و چرخه ساخت

باید دقیق بدانی:

```
Dockerfile
    ↓ docker build
Image
    ↓ docker run
Container
```

و فرق این سه تا را توضیح بدهی.

---

## 2) دستورات اصلی Dockerfile

این‌ها را باید کامل بلد باشی:

### FROM

انتخاب image پایه:

```
FROM node:20
```

مثلاً چرا از `node` یا `python` یا `ubuntu` استفاده می‌کنیم.

---

### WORKDIR

تعیین مسیر کاری:

```
WORKDIR /app
```

بدانی چرا بهتر است به جای `cd` استفاده شود.

---

### COPY و ADD

کپی کردن فایل‌ها:

```
COPY package.json .
COPY . .
```

بدانی چه چیزهایی را داخل image می‌آوری.

---

### RUN

دستورهای زمان build:

```
RUN npm install
```

بدانی تفاوتش با CMD چیست.

---

### CMD

دستور اجرای container:

```
CMD ["node","server.js"]
```

---

### ENTRYPOINT

بدانی چیست و چه فرقی با CMD دارد.

مثلاً:

```
CMD = دستور پیش‌فرض
ENTRYPOINT = برنامه اصلی container
```

---

### EXPOSE

بدانی فقط اعلام پورت است، پورت را باز نمی‌کند.

```
EXPOSE 3000
```

---

## 3) Build کردن Image

باید بلد باشی:

```
docker build -t myapp .
```

و بفهمی:

- `-t` چیست
- `.` یعنی چه
- build context چیست

---

## 4) لایه‌های Image (Layer)

این خیلی مهم است.

بدانی هر دستور Dockerfile یک layer می‌سازد:

```
FROM node
      |
WORKDIR
      |
COPY package.json
      |
RUN npm install
      |
COPY code
```

و چرا ترتیب دستورات روی cache شدن build تأثیر دارد.

مثلاً چرا این بهتر است:

```
COPY package.json .
RUN npm install
COPY . .
```

نه:

```
COPY . .
RUN npm install
```

---

## 5) حجم Image را کم کردن

باید مفهوم این‌ها را بدانی:

- استفاده از image کوچک‌تر

مثلاً:

```
node:20-alpine
```

- حذف فایل‌های اضافی
- `.dockerignore`

مثلاً:

```
node_modules
.git
.env
```

---

## 6) Environment Variables

بدانی چطور تنظیم می‌شوند:

Dockerfile:

```
ENV PORT=3000
```

یا هنگام اجرا:

```
docker run -e PORT=5000 myapp
```

---

## 7) Multi-stage Build

برای کار واقعی خیلی مهم است.

مثلاً:

```
Stage 1:
Build React app

Stage 2:
Nginx serve files
```

نمونه:

```
FROM node AS build
RUN npm run build


FROM nginx
COPY --from=build /app/dist /usr/share/nginx/html
```

---

## 8) اتصال Dockerfile با Compose

باید بدانی:

Compose می‌تواند Image آماده بگیرد:

```
image: nginx
```

یا خودش از Dockerfile بسازد:

```
build: ./backend
```

---

## 9) ساخت Image برای یک پروژه واقعی

اگر بتوانی این را انجام بدهی، یعنی Dockerfile را بلدی:

```
Node.js API
      |
 Dockerfile
      |
 backend-image
      |
 docker-compose
      |
 PostgreSQL + Redis + Nginx
```

---

به نظرم برای سطح فعلی تو، وقتی این ۵ مورد را مسلط شدی بگو Dockerfile را یاد گرفتم:

✅ FROM  
✅ COPY  
✅ RUN  
✅ CMD/ENTRYPOINT  
✅ build کردن image و اجرای container  
✅ Dockerfile داخل Compose


