## برسی اینکه درون یه docker network  چه کانتینر هایی است
---
پیش نیاز ها 
- -با inspect  ها اشنا باشید و از یکی از روش هایه  docker inspect یا docker inspect -f '{{go template}}' یا  "docker inspect <>|grep -A 7n "Container  استفاده کنید
---
مثلا ما این کار هارو کردیم

- یک `nginx` با اسم `frontend`
- یک `postgres` با اسم `database`
- هر دو باید داخل یک Network مشترک به اسم `app-net` باشند
- پسورد PostgreSQL برابر `123456` باشد
- هر دو در Background اجرا شوند
- در پایان هم باید ثابت کنی هر دو داخل `app-net` هستند

```bash
# 1) bساخت Network
docker network create app-net

# 2) ساخت PostgreSQL داخل Network
docker run -d \
  --name database \
  --network app-net \
  -e POSTGRES_PASSWORD=123456 \
  postgres

# 3) ساخت Nginx داخل همان Network
docker run -d \
  --name frontend \
  --network app-net \
  nginx


```


### حالا برایه اینکه برسی کنیم که ایا این دو کانتینر رویه شبکه app-net هستند یا نه این کارو میکنیم دو روش البته روش هایه بیشتری هم هست من دوتاشو میگم:
- ۱-رفتن درون کانتینر هایه که درون یک شبکه هستند و زدن دستور  ping anothercontainer
- ۲-زدن این کد یا زدن  docker network inspect that nework  و گشتن برایه قسمت کانتینر
```bash
docker network inspect -f '{{json .Containers}}' app-net | jq
--
out
---
{
  "203bb1e5db64432325a8188087fef861f0c3b7db83baba77d8905e712626d390": {
    "Name": "db",
    "EndpointID": "18df228b783684bd2edddad2791e4dce39fcfaf3ffa316c09329a73df4a84fb8",
    "MacAddress": "96:79:50:fc:a9:57",
    "IPv4Address": "172.18.0.2/16",
    "IPv6Address": ""
  },
  "53d6005abb8a758b85a7cc4e5239de7645d83ba91cded9d0af0babe05071fa19": {
    "Name": "web",
    "EndpointID": "2d110c5bcbd2535562d0fff9fdc9b6faddea96883b79505432ae6da0f516f6a6",
    "MacAddress": "c2:b8:23:97:a4:3b",
    "IPv4Address": "172.18.0.3/16",
    "IPv6Address": ""
  }
}
ro
```
