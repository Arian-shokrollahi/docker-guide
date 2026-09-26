## از دید یه مهندسه devops یک devops  چگونه مونت میکند و به چه چیزایی فکر میکند

---

اگر از دید یک مهندس DevOps سرِ کار نگاه کنیم، وقتی می‌خواهد یک Container را به Volume وصل کند معمولاً این Workflow را می‌رود:

1. اول مشخص می‌کند **چه دیتایی باید Persistent بماند**. مثلاً MySQL دیتایش را در این مسیر نگه می‌دارد:

```
/var/lib/mysql
```

2. بعد یک Volume می‌سازد:

```
# ساخت Volume برای دیتای MySQL
docker volume create mysql-data
```

3. بعد Container را با همان Volume اجرا می‌کند:

```
# ساخت و اجرای MySQL و اتصال Volume
docker run -d \
  --name mysql-db \
  --mount source=mysql-data,target=/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=StrongPassword \
  mysql
```

4. بعد بررسی می‌کند که Mount درست انجام شده:

```
# بررسی Mount های Container
docker inspect -f '{{.Mounts}}' mysql-db
```

5. در پروژه واقعی بعدش **Persistence را تست می‌کند**:
    - داخل MySQL یک Database می‌سازد.
    - ا-Container را حذف می‌کند.
    - ا-Container جدید را با همان Volume می‌سازد.
    - بررسی می‌کند Database هنوز هست.

ذهنیت DevOps این است:

```
Application Container
        |
        ↓
Persistent Path
        |
        ↓
Docker Volume
        |
        ↓
Data survives container replacement
```

نکته مهم: در محیط کاری واقعی معمولاً این کار را دستی با `docker run` بارها انجام نمی‌دهند؛ بیشتر با **Docker Compose، Kubernetes یا IaC** تعریف می‌کنند تا Mount قابل تکرار و مستند باشد.
