# docker search command
---

<p align="center">
	<img src="../00-images/dockersearch.png" alt="" width=1000>
</p>
  قبل شروع این رو توجه کنید:
  - زدن docker search دارید درون docker hub میگردید و اون image مدنظرتون رو میبینید
  - و با docker pull اون image  رو میارید به رپو محلیتون

---

دستور:

```bash
docker search
```

برای **جستجو کردن Imageها در Docker Hub (یا Registry پیش‌فرض Docker)** استفاده می‌شود.

یعنی قبل از اینکه یک Image را دانلود کنی (`docker pull`)، می‌توانی ببینی چه Imageهایی برای آن برنامه وجود دارد.

---

مثال:

```bash
docker search nginx
```

Docker می‌رود داخل Docker Hub و Imageهایی که اسمشان شامل `nginx` است را پیدا می‌کند.

خروجی چیزی شبیه این است:

```
NAME                         DESCRIPTION                  STARS
nginx                        Official build of Nginx       20000+
bitnami/nginx                Bitnami nginx image           300+
nginxinc/nginx-unprivileged  Unprivileged nginx            100+
```

---

توضیح ستون‌ها:

### NAME

نام Image

مثلاً:

```
nginx
```

یا:

```
bitnami/nginx
```

---

### DESCRIPTION

توضیح کوتاه درباره Image.

---

### STARS

تعداد محبوبیت Image در Docker Hub.

هرچه بیشتر باشد معمولاً نشان‌دهنده استفاده بیشتر کاربران است.

---

### OFFICIAL

اگر Image رسمی باشد:

```
[OK]
```

نمایش داده می‌شود.

مثلاً:

```
nginx     [OK]
```

یعنی این Image توسط تیم رسمی Nginx منتشر شده است.

---

### مثال واقعی Workflow:

فرض کن می‌خواهی Nginx اجرا کنی:

مرحله ۱: جستجو

```bash
docker search nginx
```

بررسی می‌کنی چه Imageهایی وجود دارد.

---

مرحله ۲: دانلود Image

```bash
docker pull nginx
```

---

مرحله ۳: دیدن Image دانلود شده

```bash
docker image ls
```

خروجی:

```
REPOSITORY   TAG      IMAGE ID
nginx        latest   abc123
```

---

مرحله ۴: ساخت Container از Image

```bash
docker run -d --name web nginx
```

---

پس در ذهن داشته باش:

```
docker search
        |
        ↓
پیدا کردن Image در Registry

docker pull
        |
        ↓
دانلود Image

docker run
        |
        ↓
ساخت و اجرای Container
```

یک نکته DevOps مهم:  
ا-`docker search` معمولاً برای **پیدا کردن اسم Image** استفاده می‌شود؛ در محیط‌های حرفه‌ای بیشتر مواقع از Docker Hub، GitHub Container Registry یا Private Registry استفاده می‌کنند و مستقیم Image موردنظر را `pull` می‌کنند.