# مونت کردن کانتینر postgres به docker volume

---
 این هم همون سناریو :

می‌خواهیم یک **PostgreSQL Container** اجرا کنیم با این شرایط:

- اسم Container: `postgres-db`
- ا-Image: `postgres`
- اجرا در Background
- مسیر دیتای PostgreSQL داخل Container:

```
/var/lib/postgresql/data
```

- اسم Volume:

```
postgres-data
```

- پسورد PostgreSQL:

```
123456
```

- ا-Mount را با مدل جدیدتر یعنی `--mount` انجام بده.

قالب کلی:

```
docker run ... \
  --name ... \
  --mount ... \
  -e ... \
  ...
```

دستورت رو بنویس و بفرست؛ من دقیق بررسی می‌کنم.

---
### حالا باید چیکار کنیم و مدل ذهنی یه مهندس
1. ببینیم کدوم دیتا است و در کجاست اون دیتایی که برایه ما مهمه
2. اسم volume چی باشه
3. اسمه کانتینر چی باشه
4. از مدل جدید mount-- یا مدل قدیمی v- میخوایم بریم
5. پسورد چی باشه 
6. و چک کردن که ایا مونت شده یا نه سپس با inspect

```shell
docker run -d \
  --name postgres-db \
  --mount source=postgres-data,target=/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=123456 \
  postgres
```

```bash
Image       → postgres

Container   → postgres-db

Volume      → postgres-data

Volume Mount
postgres-data
      ↓
/var/lib/postgresql/data

Password    → 123456

Run Mode    → Background
```

```bash
source = منبع Storage
target = مسیر داخل Container

postgres-data:/var/lib/postgresql/data
     ↑                 ↑
   Volume        Container Path
```
