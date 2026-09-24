
## به چند روش میشه image  داشت


<p align="center">
	<img src="../../00-images/bothways.png" alt="" width=1000>
</p>


ا- Docker Image را معمولاً از **دو مسیر اصلی** به دست می‌آوریم:

---

# روش 1: دریافت Image آماده با `docker pull`

در این روش یک Image که قبلاً شخص یا شرکت دیگری ساخته را از یک Registry (مثل Docker Hub) دانلود می‌کنی.

مثال:

```bash
docker pull nginx
```

اتفاقی که می‌افتد:

```
Docker Hub
     |
     |
     ↓
nginx Image
     |
     |
     ↓
Local Docker Engine
```

بعد می‌توانی ببینی:

```bash
docker image ls
```

خروجی:

```
REPOSITORY   TAG       IMAGE ID
nginx        latest    605c77e624dd
```

بعد از Image، Container می‌سازی:

```bash
docker run -d nginx
```

---

# روش 2: ساخت Image با `Dockerfile` و `docker build`

در این روش خودت Image می‌سازی.

مثلاً یک پروژه داری:

```
myapp/
│
├── Dockerfile
│
└── app.py
```

داخل Dockerfile می‌نویسی:

```Dockerfile
FROM python:3.12

WORKDIR /app

COPY app.py .

CMD ["python","app.py"]
```

بعد:

```bash
docker build -t myapp:v1 .
```

Docker این کارها را انجام می‌دهد:

```
Dockerfile
    |
    ↓
docker build
    |
    ↓
Create Layers
    |
    ↓
Image
    |
    ↓
myapp:v1
```

بعد:

```bash
docker image ls
```

می‌بینی:

```
REPOSITORY    TAG
myapp         v1
```

و اجرا:

```bash
docker run myapp:v1
```

---

## تفاوت ذهنی این دو روش:

```
روش اول:

Docker Hub
     |
 docker pull
     |
 Image آماده
     |
 Container


روش دوم:

Dockerfile
     |
 docker build
     |
 Image جدید
     |
 Container
```

---

اما در دنیای واقعی DevOps معمولاً ترکیبی هستند:

مثلاً Dockerfile تو:

```Dockerfile
FROM nginx
COPY website /usr/share/nginx/html
```

اینجا تو خودت از یک Image آماده استفاده کردی:

```
nginx Image
     |
     ↓
Dockerfile
     |
     ↓
docker build
     |
     ↓
Custom nginx Image
```

یعنی:

- `docker pull` → گرفتن Image دیگران
    
- `docker build` → ساخت Image خودت
    

این دو پایه اصلی کار با Docker Image هستند.
