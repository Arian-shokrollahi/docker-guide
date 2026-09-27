ا-`docker volume prune` برای **حذف Volumeهای بلااستفاده Docker** استفاده میشه.

دستور:

```
docker volume prune
```

یعنی Volumeهایی که **هیچ Containerی بهشون وصل نیست** پاک می‌شن.

مثلاً قبلش:

```
VOLUME NAME
db-data
old-data
cache-data
```

فرض کن:

```
db-data     → به یک Container وصل است
old-data    → استفاده نمی‌شود
cache-data  → استفاده نمی‌شود
```

بعد از:

```
docker volume prune
```

نتیجه:

```
db-data     → باقی می‌ماند
old-data    → حذف می‌شود
cache-data  → حذف می‌شود
```

نکته خیلی مهم: Volume ممکنه دیتای مهم مثل اطلاعات دیتابیس داشته باشه، پس قبلش بهتره این‌ها رو چک کنی:

```
docker volume ls
```

و در صورت نیاز:

```
docker volume inspect VOLUME_NAME
```

خلاصه:

```
docker volume prune
        ↓
Remove unused volumes
```

از بین pruneها، این یکی حساس‌تره چون ممکنه داده مهم داخل Volume ذخیره شده باشه.
