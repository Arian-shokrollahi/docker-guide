 این یه **cheat sheet جمع‌وجور و کاربردی برای `prune`** هست:

|Command|چی رو پاک می‌کنه؟|کی استفاده کنیم؟|ریسک|
|---|---|---|---|
|`docker container prune`|همه Containerهای متوقف‌شده|وقتی `docker ps -a` پر از Container قدیمیه|کم|
|`docker image prune`|فقط dangling imageها|بعد از buildهای زیاد|کم|
|`docker image prune -a`|همه Imageهای بدون استفاده|وقتی Disk پر شده و Image قدیمی زیاد داری|متوسط|
|`docker network prune`|Networkهای بلااستفاده|وقتی Network آزمایشی زیاد ساختی|کم|
|`docker volume prune`|Volumeهای بلااستفاده|فقط وقتی مطمئنی داده مهم ندارن|**زیاد**|
|`docker builder prune`|Build Cache|وقتی build cache فضای زیادی گرفته|کم تا متوسط|
|`docker system prune`|چند نوع Resource بلااستفاده باهم|Cleanup عمومی Docker|متوسط|
|`docker system prune -a`|Cleanup عمیق‌تر + unused images|کمبود جدی Disk|بیشتر|
|`docker system prune --volumes`|Cleanup عمومی + Volumeها|فقط با بررسی کامل Volumeها|**زیاد**|

### قبل از Cleanup

اول ببین Docker چقدر فضا گرفته:

```
docker system df
```

جزئیات بیشتر:

```
docker system df -v
```

بعد Resourceها رو ببین:

```
docker ps -a
docker image ls
docker volume ls
docker network ls
```

### سناریوهای واقعی

اگر فقط Containerهای Stop شده زیاد شدن:

```
docker container prune
```

اگر بعد از build زیاد، Imageهای `<none>` داری:

```
docker image prune
```

اگر Image قدیمی زیاد داری و هیچ Containerی ازشون استفاده نمی‌کنه:

```
docker image prune -a
```

اگر Network آزمایشی زیاد ساختی:

```
docker network prune
```

اگر Build Cache زیاد شده:

```
docker builder prune
```

اگر می‌خوای Cleanup عمومی انجام بدی:

```
docker system prune
```

### مهم‌ترین قانون

این رو بدون بررسی نزن:

```
docker volume prune
```

چون ممکنه Volume بلااستفاده باشه ولی هنوز دیتای مهم PostgreSQL/MySQL داخلش داشته باشی.

Workflow حرفه‌ای‌تر:

```
docker system df
        ↓
بررسی Resourceها
        ↓
prune مشخص و هدفمند
        ↓
docker system df
        ↓
مقایسه فضای آزادشده
```

برای یادگیری DevOps پیشنهاد می‌کنم به جای اینکه همیشه مستقیم `docker system prune` بزنی، اول یاد بگیری **دقیقاً کدوم Resource مشکل داره** و همون رو prune کنی.
