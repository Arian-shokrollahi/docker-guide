# بریم سراغه یه سناریو سخت تر
---
## خوده سناریو

این یکی سخت‌تره و چند نکته رو باهم تست می‌کنه:

```
Scenario 4: Application Server with Advanced Multiple Mounts

Image:
node:22-alpine

Container name:
node-multi-mount

Mount 1 - Bind Mount:
Host path:
  /root/node-app/config

Container path:
  /app/config

Mode:
  read-only

Mount 2 - Bind Mount:
Host path:
  /root/node-app/src

Container path:
  /app/src

Mode:
  read-write

Mount 3 - Volume:
Volume name:
  node-uploads

Container path:
  /app/uploads

Mount 4 - Volume:
Volume name:
  node-logs

Container path:
  /app/logs

Mount 5 - tmpfs:
Container path:
  /app/cache

tmpfs size:
  128MB

Important:
برای Bind Mountها و Volumeها از:
  --mount

استفاده کن.

برای tmpfs هم از:
  --mount type=tmpfs

استفاده کن و محدودیت حجم 128MB بده.

Port:
  Host: 3000
  Container: 3000

Environment Variable:
  NODE_ENV=production

Restart Policy:
  unless-stopped

Run mode:
  detached


```

این یکی اگر درست بزنی، یعنی `docker run` و Multiple Mount رو واقعاً خوب گرفتی.

---
### بریم سراغه حل سناریو بالا

```shell
docker run -d \
--name node-multi-mount \
--restart unless-stopped \
-e NODE_ENV=production \
--mount type=bind,source=/root/node-app/config,destination=/app/config,readonly \
--mount type=bind,source=/root/node-app/src,destination=/app/src \
--mount type=volume,source=node-uploads,destination=/app/uploads \
--mount type=volume,source=node-logs,destination=/app/logs \
--mount type=tmpfs,destination=/app/cache,tmpfs-size=128m \
-p 3000:3000 \
node:22-alpine
```

### اینم از توضیحاته این قسمت
- ۱- docker run -d اجرایه کانتینر در پس زمینه
- ۲-name-- استفاده از این فلگ برایه مشخص کردن نام کانتینر
- ۳- restart-- اگر کانتینر به هر دلیلی متوقف شد یا سیستم ری‌استارت شد، Docker دوباره آن را بالا می‌آورد؛ مگر اینکه خودت دستی Stop کرده باشی.
- ۴- e- یک متغیر محیطی  environment variable داخل کانتینر میسازد
- ۵-بقیشم که دیگه مانت کردن در مدل هایه مختلف bind volume tmpfs
- و در مانت اخر سایز داده که میگه  اون مسیر کانتینر رو داخل رم قرار بده و حداکثر حجمش 128 مگابایت
- و وصل کردن سیستم یا هاست به کانتینر با پورت 3000
