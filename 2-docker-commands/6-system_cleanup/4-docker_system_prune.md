ا-`docker system prune` برای **پاک‌سازی منابع بلااستفاده Docker** استفاده میشه.

یعنی Docker چیزهایی که دیگه استفاده نمی‌شن رو پیدا می‌کنه و پاک می‌کنه تا Disk آزاد بشه.

```
docker system prune
```

معمولاً این‌ها رو پاک می‌کنه:

```
Stopped Containers
Unused Networks
Dangling Images
Build Cache
```

مثلاً اگر چندتا Container قدیمی Stop شده داشته باشی، چندتا Network بلااستفاده و Cache ساخت Image هم جمع شده باشه، این دستور می‌تونه همه رو یکجا پاک کنه.

قبل از حذف معمولاً ازت تأیید می‌گیره:

```
WARNING! This will remove:
  - all stopped containers
  - all networks not used by at least one container
  - all dangling images
  - unused build cache

Are you sure you want to continue? [y/N]
```

اگر `y` بزنی، پاکسازی انجام میشه.

یک نکته مهم: به‌صورت پیش‌فرض **همه Imageهای بلااستفاده** رو حذف نمی‌کنه؛ بیشتر `dangling images` رو پاک می‌کنه.

اگر بخوای عمیق‌تر پاکسازی کنی:

```
docker system prune -a
```

این حالت Imageهایی که هیچ Containerی ازشون استفاده نمی‌کنه رو هم پاک می‌کنه.

و اگر بخوای Volumeهای بلااستفاده هم وارد پاکسازی بشن:

```
docker system prune --volumes
```

خلاصه:

```
docker system prune
        ↓
General Docker Cleanup
        ↓
Stopped Containers
Unused Networks
Dangling Images
Build Cache
```

قبل از `prune` بهتره معمولاً این رو بزنی:

```
docker system df
```

تا اول ببینی Docker چقدر فضا گرفته و چه مقدارش قابل آزاد شدنه.
