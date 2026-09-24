# دستور docker rename چیه و فرقش با docker tag  چیه؟

---

<p>
	<img src="../../00-images/dockerrename.png" alt="v" width=1000>
</p>

حتماً. این دوتا دستور از نظر اسم ممکنه شبیه به نظر بیان، ولی روی **دو چیز کاملاً متفاوت** کار می‌کنن:

### ا-`docker rename` — تغییر نام Container

ا-`docker rename` برای تغییر **اسم یک کانتینر موجود** استفاده می‌شود.

ساختار:

```bash
docker rename OLD_NAME NEW_NAME
```

مثلاً اول کانتینرها را می‌بینیم:

```bash
docker ps
```

فرض کن داریم:

```text
CONTAINER ID   IMAGE   NAMES
a8f3c92d12ab   nginx   old-web
```

اسمش را عوض می‌کنیم:

```bash
docker rename old-web new-web
```

حالا:

```bash
docker ps
```

می‌شود:

```text
CONTAINER ID   IMAGE   NAMES
a8f3c92d12ab   nginx   new-web
```

یعنی خود Container همان Container قبلی است؛ فقط **اسمش عوض شده**.

نکته مهم اینکه حتی لازم نیست کانتینر را Stop کنی. روی کانتینر Running هم می‌توانی:

```bash
docker rename old-web new-web
```

اجرا کنی.

---

### ا-`docker tag` — دادن نام/Tag جدید به Image

این دستور روی **Image** کار می‌کند، نه Container.

ساختار کلی:

```bash
docker tag SOURCE_IMAGE TARGET_IMAGE
```

مثلاً این Image را داریم:

```bash
docker images
```

```text
REPOSITORY   TAG       IMAGE ID
nginx        latest    f4a8d2c1e6b3
```

می‌زنیم:

```bash
docker tag nginx:latest my-nginx:v1.0
```

دوباره:

```bash
docker images
```

ممکن است ببینی:

```text
REPOSITORY   TAG       IMAGE ID
nginx        latest    f4a8d2c1e6b3
my-nginx     v1.0      f4a8d2c1e6b3
```

به `IMAGE ID` دقت کن:

```text
nginx:latest     ───┐
                   ├──> f4a8d2c1e6b3
my-nginx:v1.0    ───┘
```

یعنی `docker tag` یک Image جدید را از صفر Build نکرده و Image را هم واقعاً کپی نکرده؛ یک **reference جدید** برای همان Image ساخته است.

### چرا اصلاً `docker tag` لازم داریم؟

یکی از مهم‌ترین کاربردهایش Versioning است.

مثلاً Image پروژه‌ات:

```bash
myapp:latest
```

می‌توانی برای Release مشخص به آن Tag بدهی:

```bash
docker tag myapp:latest myapp:v1.0
```

یا:

```bash
docker tag myapp:latest myapp:v1.1
```

یا قبل از Push کردن به Registry:

```bash
docker tag myapp:v1.0 username/myapp:v1.0
```

بعد:

```bash
docker push username/myapp:v1.0
```

پس `docker tag` در workflowهای CI/CD و Registry خیلی مهم می‌شود.

### تفاوت اصلی را این‌طوری حفظ کن

```text
              Docker
                 │
        ┌────────┴────────┐
        ↓                 ↓
      IMAGE           CONTAINER
        │                 │
   docker tag        docker rename
        │                 │
        ↓                 ↓
 نام/Tag جدید         نام جدید
 برای Image          برای Container
```

مثلاً:

```bash
docker rename web1 production-web
```

یعنی:

> اسم **Container** را عوض کن.

ولی:

```bash
docker tag nginx:latest nginx:v1
```

یعنی:

> برای **Image** یک reference/tag دیگر بساز.

یک نکته خیلی مهم هم این است که این دو را با `docker image tag` اشتباه نگیری. این دو دستور عملاً یک کار می‌کنند:

```bash
docker tag nginx:latest my-nginx:v1
```

و:

```bash
docker image tag nginx:latest my-nginx:v1
```

دومی فقط شکل ساختاریافته‌تر دستور در CLI داکر است.

برای مسیر یادگیری تو، بعد از `docker tag` بهتره مستقیم بریم سراغ **ساختار کامل نام Image یعنی `registry/repository:tag`**؛ چون اونجا دقیقاً متوجه می‌شی چرا Tag کردن قبل از `docker push` این‌قدر مهمه.
