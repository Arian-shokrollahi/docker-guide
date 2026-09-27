ا-`docker container prune` برای **حذف همه Containerهایی که در حالت Stop هستن** استفاده میشه.

دستور:

```
docker container prune
```

قبل از حذف ازت تأیید می‌گیره:

```
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N]
```

اگر `y` بزنی، همه Containerهای متوقف‌شده حذف می‌شن.

مثلاً قبلش:

```
docker ps -a
```

خروجی:

```
NAME        STATUS
web         Up 10 minutes
db          Exited (0)
test        Exited (1)
```

بعد:

```
docker container prune
```

نتیجه:

```
web   → باقی می‌مونه
db    → حذف میشه
test  → حذف میشه
```

پس خلاصه:

```
docker container prune
        ↓
Remove all stopped containers
```

ا-Containerهای Running رو حذف نمی‌کنه.
