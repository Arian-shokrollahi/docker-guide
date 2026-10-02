# بریم سراغه compose key سطح بالا toplevel شبکه network
---
ا-`networks` یکی از **Top-level key**های مهم در Docker Compose است.

کارش اینه که **شبکه‌های پروژه را تعریف کند** تا سرویس‌ها بتوانند از طریق آن شبکه با هم ارتباط داشته باشند.

مثلاً:

```
services:
  backend:
    image: my-backend
    networks:
      - app-net

  db:
    image: postgres
    networks:
      - app-net

networks:
  app-net:
```

اینجا دوباره مثل `volumes` دو تا `networks` داریم:

- `services → backend → networks`  
    یعنی این سرویس به چه شبکه‌ای وصل شود.
    
- Top-level `networks:`  
    یعنی خود شبکه‌ی پروژه را تعریف می‌کنیم.
    

پس خیلی ساده:

**Top-level `networks` → محل تعریف شبکه‌های پروژه است.**

و داخل هر سرویس:

**Service-level `networks` → مشخص می‌کند آن سرویس عضو کدام شبکه باشد.**

مثال ذهنی:

```
backend ─┐
         ├── app-net
database ┘
```

مزیت مهمش اینه که سرویس‌ها داخل همان network می‌توانند با **اسم سرویس** همدیگر را پیدا کنند. مثلاً `backend` می‌تواند به جای IP به `db` وصل شود.

برای جزوه‌ات این جمله خوبه:

ا-**`networks` در Top-level برای تعریف شبکه‌های پروژه است و در Service-level برای مشخص کردن اتصال هر سرویس به آن شبکه استفاده می‌شود.**

---
### چند باری تکرار کردم ولی باز توجه کنید به این دو موضوع مهم
- ۱-toplevel networks برای تعریف شبکه های پروژه 
- ۲-service level networks برای تعریف این که هر سرویس عضو چه شبکه ای استL
