## انواعه mount کردن data درون داکر
---
##### تو Docker برای **mount کردن data** سه مدل اصلی که باید خوب بلد باشی اینان:
| نوع | داده کجا نگه‌داری میشه؟ | کاربرد |
|---|---|---|
| **Volume** | داخل فضای مدیریت‌شده Docker | بهترین گزینه برای دیتای دائمی |
| **Bind Mount** | یک مسیر مشخص روی Host | وقتی می‌خوای فایل Host مستقیم داخل Container دیده بشه |
| **tmpfs** | فقط داخل RAM | داده موقت و حساس |
##### و برایه اسم گذاری هم دو مدل در مدل volume mount
- ۱-Named volume
- ۲-Anonymous volume 
- ۳- حالت دیگه ای هم هست که میاید اون رو درون فایل yaml. -->docker compose مینویسید
---
## 1- بریم اول سراغه volume mount 
- چه مراحلی دارد
- ۱- ساخت volume  با دستور `docker volume create volume-name`
- ۲- و مدل مونت کردن دیتا با docker volume دو مدل : برای استفاده از **Volume** در Docker معمولاً دو روش اصلی داری:

1. با `-v` یا `--volume`:

```bash
docker run -d \
  -v mydata:/app/data \
  nginx
```

2. با `--mount`:

```bash
docker run -d \
  --mount type=volume,source=mydata,target=/app/data \
  nginx
```

هر دو تقریباً یک کار می‌کنن:

```text
mydata  ─────►  /app/data
Volume          Container
```

تفاوت اصلی اینه که `-v` کوتاه‌تر و سریع‌تره، ولی `--mount` خواناتر و دقیق‌تره و برای محیط حرفه‌ای بهتره.

مثلاً:

```bash
-v mydata:/app/data
```

همون مفهوم اینه:

```bash
--mount type=volume,source=mydata,target=/app/data
```

فقط حواست باشه این «دو نوع Volume» نیست؛ **دو syntax برای mount کردن Volume** هست.

---

### وقتی شما میاید این مونت(docker named volume) رو انجام میدید اون دیتایی که از کانتینر مونت میشه در کدوم مسیر قابل دیدن 

- معمولا در مسیر var/lib/docker/volumes/ است ولی اگر درون اون مسیر نبود راهه بهتر از دستور inspect استفاده کنید و از اون volume بیاید یه inspect بگیرید و ببنید mount pointesh کجاست
- برای اینکه بتونید مسیری که دیتا شما به اون در اون docker volume  مربوطه است رو ببینید باید این دستور رو بزنید:
```
1-docker container inspect -f '{{json .Mounts}}' containernameor id | jq
or
2-docker inspect -f '{{json .Mounts}}' containername or id | jq
---
for ex output
---
[
  {
    "Type": "volume",
    "Name": "postgres-db",
    "Source": "/var/snap/docker/common/var-lib-docker/volumes/postgres-db/_data",
    "Destination": "/var/lib/postgresql",
    "Driver": "local",
    "Mode": "z",
    "RW": true,
    "Propagation": ""
  }
]

```
 - الان source  میشه اون جایی که رویه سیستم ماست 
 - و destination اون مقصد اون کانتینری که مونته به سیستم ما
---
### اون قضه نام گذاری که دو مدل بود چیست:

از نظر **نام‌گذاری**: ما میتونیم از دو روش استفاده کنیم 

```
Volume
├── Named
└── Anonymous
```
- ۱-اگر در هنگام ساخت volume  اسم انتخاب کنی volume named میشه
- ۲-اگر در هنگام ساخت volume اسم انتخاب نکنی anonymous  named میشه
```
docker volume ls 
---
root@alfamachine:~# docker volume ls
DRIVER    VOLUME NAME
local     eeb1a35e2db4e4320a0b00d57b96b9153268e958d694adb2dfd70ab60afc96fa
local     postgres-db

```

---
### 2- بریم سراغه مدل بعدی مانت کردن دیتا ها با مدل bind mount
- ا-**Bind Mount** یکی از روش‌های mount کردن دیتا در Docker است که در آن **یک مسیر واقعی از Host را مستقیم داخل Container وصل می‌کنی**.
- برخلاف Volume که Docker خودش محل ذخیره را مدیریت می‌کند، در Bind Mount تو خودت مسیر Host را انتخاب می‌کنی.

ساختار:

```
HOST                         CONTAINER

/home/arian/project  ─────►  /app
```

یعنی:

- روی Host داری:

```
/home/arian/project
```

- داخل Container دیده می‌شود:

```
/app
```
---
### با چند روش میتونیم bind mount بسازیم --> bind mount  هم به دو روش است
- 1-با فلگ v- همون مدل سخت تره
- ۲-با فلگ mount-- همون مدله که خوانایی بیشتری داشت
‍‍‍‍‍
با هر دو syntax می‌نویسیم:

### مدل حرفه‌ای با `--mount`

```
docker run -d \
  --name app \
  --mount type=bind,source=/root/a,target=/app/data \
  nginx
```

معادلش با `-v`:

```
docker run -d \
  --name app \
  -v /root/a:/app/data \
  nginx
```

یعنی:

```
-v SOURCE:TARGET

/root/a  ─────────►  /app/data
 Host              Container
```


```
Host                       Container

/root/a   ───────────────► /app/data
```

### اگر بخواهی به صورت bind  مانت شه و read only باشه
```
--mount:
type=bind,source=/host/path,target=/container/path,readonly

-v:
 /host/path:/container/path:ro
```
----
## ۳- بریم سراغه مدل اخره مونت کردن دیتا مدل tmpfs
##### اول tmpfs bind mount چیست؟


در tmpfs، دیتا **روی RAM سیستم Host** ذخیره می‌شود، نه روی دیسک.

یعنی:

```
Host RAM
   │
   ▼
Container
/app/temp
```

وقتی Container حذف یا متوقف شود، این دیتا از بین می‌رود.


مدلی که در عمل بیشتر استفاده می‌شود همین است:

```
docker run -d \
  --name app \
  --tmpfs /app/temp \
  nginx
```

یعنی یک مسیر موقت داخل Container می‌سازی که روی RAM قرار دارد.

اما در پروژه واقعی معمولاً کمی محدودش می‌کنند:

```
docker run -d \
  --name app \
  --tmpfs /app/cache:size=100m \
  nginx
```

اینجا:

```
/app/cache
      |
      ▼
    RAM
      |
      ▼
حداکثر 100MB
```







---

#### خلاصه انگلیسی
```
Volume:
"Docker, save my data"

Bind Mount:
"I choose the Host folder"

tmpfs:
"Keep this only in memory"
```
