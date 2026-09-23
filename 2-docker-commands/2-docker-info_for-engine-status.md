## دستور docker info برایه دیدن وضعیت docker engine

---

ا-ش`docker info` اطلاعات کامل‌تری از وضعیت Docker Engine روی سیستم بهت می‌ده.

دستور:

```bash
docker info
```

نمونه‌ی خروجی ممکنه شبیه این باشه:

```text
Client:
 Version:    27.5.1
 Context:    default
 Debug Mode: false

Server:
 Containers: 3
  Running: 1
  Paused: 0
  Stopped: 2
 Images: 8
 Server Version: 27.5.1
 Storage Driver: overlay2
 Operating System: Ubuntu 24.04
 CPUs: 8
 Total Memory: 15.5GiB
```

بررسی کوتاه:

`Containers: 3`

یعنی در مجموع ۳ Container داری.

`Running: 1`

یعنی ۱ Container الان در حال اجراست.

`Stopped: 2`

یعنی ۲ Container متوقف شده.

`Images: 8`

یعنی ۸ Image روی سیستم ذخیره شده.

`Server Version`

نسخه‌ی Docker Engine رو نشون می‌ده.

`Storage Driver`

مشخص می‌کنه Docker برای ذخیره‌سازی لایه‌های Image و Container از چه روشی استفاده می‌کنه.

`CPUs` و `Total Memory`

منابع سیستمی که Docker در اختیار داره رو نشون می‌ده.

پس خیلی خلاصه:

```text
docker --version
=
فقط نسخه Docker

docker info
=
وضعیت و اطلاعات کامل Docker Engine
```

برای شروع، مهم‌ترین بخش‌هایی که از `docker info` باید بلد باشی این‌هاست:

```text
Containers
Images
Server Version
Storage Driver
CPUs
Total Memory
```

بعد از این، منطقی‌ترین دستور برای ادامه‌ی مسیرت `docker images` هست.

---
# در دستور docker info  چه چیزهایی رو باید خیلی خوب بلد باشم

برای شروع لازم نیست کل خروجی `docker info` رو حفظ کنی. این بخش‌ها مهم‌ترن:

- `Containers`  
    تعداد کل Containerها رو نشون می‌ده.
    
- `Running`  
    چند Container الان در حال اجراست.
    
- `Stopped`  
    چند Container متوقف شده.
    
- `Images`  
    تعداد Imageهای موجود روی سیستم.
    
- `Server Version`  
    نسخه‌ی Docker Engine.
    
- `Storage Driver`  
    روش ذخیره‌سازی Docker. روی Linux معمولاً چیزی مثل `overlay2` می‌بینی.
    
- `CPUs`  
    تعداد CPUهایی که Docker می‌تونه استفاده کنه.
    
- `Total Memory`  
    مقدار RAM در دسترس Docker.
    
- `Docker Root Dir`  
    مسیر اصلی فایل‌های Docker روی سیستم. معمولاً:
    

```text
/var/lib/docker
```

- `Operating System`  
    سیستم‌عاملی که Docker Engine روی اون اجرا میشه.
    

برای الان این 6 مورد رو بیشتر تمرکز کن:

```text
Containers
Running
Images
Server Version
Storage Driver
Docker Root Dir
```

یه خلاصه‌ی خیلی ساده:

```text
docker info
```

بهت جواب این سؤال‌ها رو می‌ده:

```text
Docker سالمه؟
چند Container دارم؟
چند Image دارم؟
Engine چه نسخه‌ایه؟
Docker اطلاعاتش رو کجا ذخیره می‌کنه؟
از چه Storage Driver استفاده می‌کنه؟
```

برای مسیر DevOpsت، بعداً بخش‌هایی مثل `Cgroup Driver`، `Logging Driver` و `Registry` هم مهم می‌شن، ولی الان لازم نیست درگیرشون بشی.
