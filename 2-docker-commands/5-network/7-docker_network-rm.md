ا-`docker network rm` برای **حذف کردن یک Docker Network** استفاده میشه.

ساختار:

```
docker network rm NETWORK_NAME
```

مثال:

```
docker network rm company-net
```

یعنی Network به اسم `company-net` حذف میشه.

نکته مهم: اگر Container فعالی هنوز به اون Network وصل باشه، Docker اجازه حذف نمی‌ده. اول باید Containerها رو Disconnect کنی یا حذفشون کنی.

مثلاً:

```
docker network disconnect company-net frontend
docker network disconnect company-net database

docker network rm company-net
```

برای حذف چند Network با هم:

```
docker network rm net1 net2 net3
```

خلاصه:

```
docker network rm
        ↓
حذف یک Docker Network
```

و اگر بخوای Networkهای بدون استفاده رو پاک کنی:

```
docker network prune
```
