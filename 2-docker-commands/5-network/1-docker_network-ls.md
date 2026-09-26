ا-`docker network ls` برای **نمایش لیست Networkهای موجود در Docker** استفاده می‌شود.

```
docker network ls
```

نمونه خروجی:

```
NETWORK ID     NAME        DRIVER    SCOPE
2c4f7f9b3a21   bridge      bridge    local
f8c19d3aa521   host        host      local
7a8b0d21cd44   none        null      local
9db21ac761f2   backend     bridge    local
```

معنی ستون‌ها:

- `NETWORK ID` → شناسه Network
- `NAME` → اسم Network
- `DRIVER` → نوع Network
- `SCOPE` → محدوده Network

مقادیر مهمی که ممکن است در `DRIVER` ببینی:

```
bridge   → رایج‌ترین نوع برای ارتباط Containerها روی یک Host
host     → استفاده مستقیم از Network خود Host
null     → بدون Network
overlay  → برای ارتباط بین چند Docker Host
macvlan  → دادن حضور مستقیم‌تر Container در شبکه فیزیکی
```

در ستون `NAME` معمولاً این سه Network پیش‌فرض را می‌بینی:

```
bridge
host
none
```

و هر Network دیگری مثل:

```
backend
frontend
database-net
```

معمولاً Networkی است که خودت ساخته‌ای.

در `SCOPE` بیشتر اوقات می‌بینی:

```
local
```

یعنی Network فقط روی همین Docker Host است.

برای بعضی Networkهای چند Host ممکن است:

```
swarm
```

ببینی.

پس این خروجی:

```
9db21ac761f2   backend   bridge   local
```

یعنی:

> یک Network به نام `backend` داریم که از Driver نوع `bridge` استفاده می‌کند و فقط روی همین Docker Host وجود دارد.

خلاصه:

```
docker network ls
↓
لیست Networkها
↓
ID | Name | Driver | Scope
```
