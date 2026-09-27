ا-`docker image prune` برای **پاک کردن Imageهای بلااستفاده Docker** استفاده میشه.

حالت معمولی:

```
docker image prune
```

این دستور فقط **dangling images** رو حذف می‌کنه؛ یعنی Imageهایی که دیگه Tag ندارن و معمولاً از buildهای قبلی باقی موندن.

مثلاً:

```
REPOSITORY   TAG       IMAGE ID
nginx        latest    abc123
<none>       <none>    def456
```

اینجا این Image:

```
<none> <none> def456
```

یک dangling image هست و با این دستور حذف میشه:

```
docker image prune
```

اگر بخوای **همه Imageهایی که هیچ Containerی ازشون استفاده نمی‌کنه** حذف بشن:

```
docker image prune -a
```

فرق مهم:

```
docker image prune
→ فقط dangling images

docker image prune -a
→ همه unused images
```

قبل از حذف هم معمولاً تأیید می‌گیره.

برای احتیاط قبلش اینو ببین:

```
docker image ls
```

و اگر می‌خوای بفهمی چه مقدار فضا آزاد میشه:

```
docker system df
```

پس خلاصه:

```
docker image prune
        ↓
Cleanup unused/dangling images
```
