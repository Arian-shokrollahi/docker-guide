# بریم سراغه اولین سناریو از نوشتن docker compose فایل که درون این سناریو ما میفهمیم چجوری میتونیم مانت bind volume tmpfs رو ببینیم
---
## خوده سناریو به این صورت است
‍‍‍```
Scenario: Multiple Mounts with Docker Compose

Service name:
  web

Image:
  nginx:latest

Container name:
  nginx-compose-test

Mount 1 - Bind Mount:
Host path:
  /root/nginx-project/html

Container path:
  /usr/share/nginx/html

Mode:
  read-only

Mount 2 - Volume:
Volume name:
  nginx-logs

Container path:
  /var/log/nginx

Mount 3 - tmpfs:
Container path:
  /var/cache/nginx

Port:
  8080:80

Restart policy:
  unless-stopped
```
---
### برین سراغه نوشته کامپوس فایل 
- باید فایل درست کنید به اسم compose.yaml
- سپس برید سراغه اینکه اون ویرایشگر متنی که دوست دارید و این فایل رو انتخاب کنید و درون اون بنویسید کانفیگ هایی که میخواید باهاش docker compose اون سرویس رو اون طوری که دوست دارید اجرا کنید

خیلی نزدیکه. فقط سه ایراد داری:

1. زیر `services:` باید اسم سرویس بیاد، مثلاً `web:`
2. باید `volumes:` باشه، نه `volume:`
3. پایین فایل باید named volume رو تعریف کنی.

```yaml
services:
  web:
    image: nginx:latest
    container_name: nginx-compose-test

    volumes:
      - /root/nginx-project/html:/usr/share/nginx/html:ro
      - nginx-logs:/var/log/nginx

    tmpfs:
      - /var/cache/nginx

    ports:
      - "8080:80"

    restart: unless-stopped

volumes:
  nginx-logs:
```

ساختارش اینطوریه:

```text
services
└── web
    ├── image
    ├── container_name
    ├── volumes
    ├── tmpfs
    ├── ports
    └── restart

volumes
└── nginx-logs
```

#### چیزایی که واقعا باید دقت کنید
- یک  اون کلید که درونش bind volume رو میزارید volumes است نه volume
- درون bindmount شما مسیر هاست یا سیستمتون که میخواید روش مانتینگ انجام شه رو میدید و برایه volume شما اسمه اون ولوم رو در اول میدید و در اخر هم باید اون والیوم رو تعریف کنید
- مدل tmpfs هم چون سورسش RAM دیگه سور نمیدی فقط اون مسیری رو میدی که میخوای بره تو رم از کانتینر
