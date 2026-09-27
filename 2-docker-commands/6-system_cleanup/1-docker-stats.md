 ا-`docker stats` برای **نمایش لحظه‌ای مصرف منابع Containerهای در حال اجرا** استفاده میشه؛ تقریباً مثل یک `top` مخصوص Containerها.

```
docker stats
```

مثلاً خروجی می‌تونه شبیه این باشه:

```
CONTAINER ID   NAME       CPU %   MEM USAGE / LIMIT     MEM %   NET I/O             BLOCK I/O        PIDS
a12bc34de56f   web        0.25%   24.5MiB / 7.70GiB    0.31%   12.4kB / 8.2kB   1.2MB / 0B       5
b98fe12ac321   database   1.42%   185MiB / 7.70GiB     2.35%   42kB / 35kB       8.4MB / 2.1MB   17
```

هر ستون یعنی:

|ستون|معنی|
|---|---|
|`CONTAINER ID`|ID کانتینر|
|`NAME`|اسم کانتینر|
|`CPU %`|درصد CPU که Container مصرف می‌کنه|
|`MEM USAGE / LIMIT`|RAM مصرفی / حداکثر RAM قابل استفاده|
|`MEM %`|درصد RAM مصرف‌شده|
|`NET I/O`|مقدار دیتای دریافت‌شده و ارسال‌شده از Network|
|`BLOCK I/O`|مقدار Read/Write روی Storage/Disk|
|`PIDS`|تعداد Processهای داخل Container|

مثلاً این:

```
database   1.42%   185MiB / 7.70GiB   2.35%
```

یعنی Container به اسم `database` حدود `1.42%` از CPU استفاده می‌کنه و `185MiB` RAM مصرف کرده.

در `NET I/O`:

```
42kB / 35kB
```

تقریباً یعنی:

```
42kB received
35kB sent
```

و در `BLOCK I/O`:

```
8.4MB / 2.1MB
```

میزان خواندن/نوشتن روی Storage رو نشون میده.

اگر فقط یک Container رو بخوای Monitor کنی:

```
docker stats web
```

اگر چندتا خاص رو بخوای:

```
docker stats web database
```

و اگر فقط یک‌بار خروجی بگیری و نخواهی دائماً Refresh بشه:

```
docker stats --no-stream
```

پس ذهنت این شکلی باشه:

```
docker stats
      ↓
Container resource monitoring
      ↓
CPU
RAM
Network I/O
Disk I/O
Processes
```

## چرا این دستور برای مهندسان devops  مهمه
این دستور در DevOps خیلی به درد می‌خوره وقتی مثلاً می‌گی «چرا سایت کند شده؟» و سریع می‌خوای ببینی کدوم Container CPU یا RAM زیادی مصرف می‌کنه.
