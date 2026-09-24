# چرا ما از دستور docker pull  استفاده میکنیم

---
**سؤال: چرا از command `docker pull` استفاده می‌کنیم؟**

**جواب:**

ما از `docker pull` استفاده می‌کنیم تا یک **Docker Image آماده را از یک Docker Registry دریافت کنیم و روی سیستم خودمان ذخیره کنیم** تا بتوانیم بعداً از آن برای ساخت و اجرای Container استفاده کنیم.

مثلاً فرض کن می‌خواهیم یک وب‌سرور Nginx اجرا کنیم.

اول Image آن را نداریم:

```
docker image ls
# List local images
```

خروجی:

```
REPOSITORY   TAG
```

هیچ Imageای وجود ندارد.

حالا:

```
docker pull nginx
# Download nginx image from Docker Registry
```

ا-Docker می‌رود به:

```
Docker Hub (Registry)
        |
        | Download
        v
nginx Image
        |
        v
Local Docker Engine
```

حالا دوباره چک می‌کنیم:

```
docker image ls
# List local images
```

خروجی:

```
REPOSITORY   TAG
nginx        latest
```

حالا Image روی سیستم ما وجود دارد و می‌توانیم از آن Container بسازیم:

```
docker run nginx
# Create and start a container from nginx image
```

پس دلیل استفاده از `docker pull`:

```
We need an image
        |
        v
Image is not available locally
        |
        v
Pull it from Registry
        |
        v
Use it to create containers
```

یک جواب کوتاه برای مصاحبه DevOps:

> We use `docker pull` to download Docker images from a registry to the local Docker Engine so that we can create and run containers from those images.


