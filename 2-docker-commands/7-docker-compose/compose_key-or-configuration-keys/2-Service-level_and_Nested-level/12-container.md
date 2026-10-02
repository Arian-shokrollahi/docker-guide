# container key
---
ا-`container_name` یک **Service-level key** است که باهاش اسم مشخص و دلخواه برای کانتینر تعیین می‌کنی.

مثلاً:

```
services:
  backend:
    image: my-backend
    container_name: backend-prod
```

در این حالت اسم کانتینر می‌شود:

```
backend-prod
```

و مثلاً می‌تونی بنویسی:

```
docker logs backend-prod
```

یا:

```
docker exec -it backend-prod sh
```

اگر `container_name` نگذاری، Docker Compose خودش اسم می‌سازد، معمولاً چیزی شبیه:

```
project-backend-1
```

پس برای جزوه:

ا-**`container_name` → برای تعیین نام ثابت و دلخواه کانتینر استفاده می‌شود.**

نکته: در پروژه‌های حرفه‌ای همیشه لازم نیست `container_name` بگذاری؛ Compose خودش naming را مدیریت می‌کند و برای scale کردن سرویس‌ها هم معمولاً بهتر است اسم را دستی ثابت نکنی.container_name یک Service-level key است که باهاش اسم مشخص و دلخواه برای کانتینر تعیین می‌کنی.
مثلاً:
services:
  backend:
    image: my-backend
    container_name: backend-prod

در این حالت اسم کانتینر می‌شود:
backend-prod

و مثلاً می‌تونی بنویسی:
docker logs backend-prod

یا:
docker exec -it backend-prod sh

اگر container_name نگذاری، Docker Compose خودش اسم می‌سازد، معمولاً چیزی شبیه:
project-backend-1

پس برای جزوه:
ا-container_name → برای تعیین نام ثابت و دلخواه کانتینر استفاده می‌شود.
نکته: در پروژه‌های حرفه‌ای همیشه لازم نیست container_name بگذاری؛ Compose خودش naming را مدیریت می‌کند و برای scale کردن سرویس‌ها هم معمولاً بهتر است اسم را دستی ثابت نکنی.
