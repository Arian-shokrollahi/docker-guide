ا-`docker system df` برای اینه که ببینی **Docker چقدر از فضای Disk رو مصرف کرده** و این فضا بین Imageها، Containerها، Volumeها و Build Cache چطور تقسیم شده.

<p align="center">
	<img src="../00-images/dockersystemdf.png" alt="" width=1000>
</p>

دستور:

```
docker system df
```

نمونه خروجی:

```
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          8         3         2.4GB     1.1GB (45%)
Containers      5         2         350MB     200MB (57%)
Local Volumes   4         2         1.8GB     600MB (33%)
Build Cache     12        0         900MB     900MB
```

معنی ستون‌ها:

|ستون|معنی|
|---|---|
|`TYPE`|نوع Resource|
|`TOTAL`|تعداد کل Resourceها|
|`ACTIVE`|چندتا واقعاً در حال استفاده هستن|
|`SIZE`|مجموع فضایی که گرفتن|
|`RECLAIMABLE`|فضایی که احتمالاً می‌تونی آزادش کنی|

مثلاً این:

```
Images   8   3   2.4GB   1.1GB (45%)
```

یعنی:

- 8 تا Image داری
- 3 تاش فعلاً مورد استفاده‌ست
- مجموعاً 2.4GB فضا گرفتن
- حدود 1.1GB قابل پاکسازی هست

اگر جزئیات بیشتری بخوای:

```
docker system df -v
```

این حالت دقیق‌تر نشون میده هر Image یا Volume چقدر فضا گرفته.

ذهنی خیلی خلاصه:

```
docker system df
        ↓
Docker disk usage
        ↓
Images
Containers
Volumes
Build Cache
```

برای DevOps خیلی کاربردیه وقتی می‌بینی Disk سرور داره پر میشه و می‌خوای بفهمی مشکل از کدوم بخش Dockerه.
