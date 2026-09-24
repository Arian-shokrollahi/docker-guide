# چجوری میتونیم اون نسخه دلخواهمون رو از image دانلود کنیم از اون ریجیستری
---
برای گرفتن نسخه دلخواه (`Tag`) از یک Image در `docker pull` باید اسم Image را همراه با **Tag** بنویسیم.

ساختار کلی:

```
docker pull IMAGE_NAME:TAG
# Download a specific version of an image from a registry
```

مثلاً:

```
docker pull nginx:1.27
# Download nginx version 1.27
```

اینجا:

```
nginx
=
Image Name

1.27
=
Tag (Version)
```

---

چند مثال دیگر:

گرفتن Ubuntu نسخه 24.04:

```
docker pull ubuntu:24.04
# Download Ubuntu 24.04 image
```

گرفتن Python نسخه 3.12:

```
docker pull python:3.12
# Download Python 3.12 image
```

---

اگر Tag ننویسی:

```
docker pull nginx
```

Docker معمولاً این را در نظر می‌گیرد:

```
docker pull nginx:latest
```

یعنی آخرین نسخه‌ای که با Tag `latest` منتشر شده.

---

برای دیدن Tagهای موجود یک Image معمولاً باید به Registry (مثلاً Docker Hub) مراجعه کنی و نسخه‌های موجود را ببینی.

مثلاً برای Nginx ممکن است داشته باشی:

```
nginx:latest
nginx:1.27
nginx:1.26
nginx:alpine
```

پس:

```
docker pull nginx:alpine
```

نسخه‌ای از Nginx را می‌گیرد که بر پایه Alpine Linux ساخته شده.

---

نکته مهم برای DevOps:

در محیط Production معمولاً بهتر است از `latest` استفاده نکنی:

بد:

```
docker pull nginx:latest
```

بهتر:

```
docker pull nginx:1.27.3
```

چون نسخه مشخص است و بعداً با تغییر ناگهانی Image، سرویس تو تغییر نمی‌کند.

خلاصه:

```
docker pull image:tag

image = نام برنامه
tag   = نسخه یا نوع Image
```

مثال نهایی:

```
docker pull nginx:1.27.3
```

یعنی:

«از Registry، Image مربوط به Nginx با نسخه 1.27.3 را دانلود ک
