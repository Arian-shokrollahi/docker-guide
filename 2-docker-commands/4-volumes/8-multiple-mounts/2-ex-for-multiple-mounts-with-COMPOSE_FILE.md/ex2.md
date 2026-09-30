# بریم یه سناریو یه پله سخت تر از سناریو قبلی
---
## سناریو این است:
```
Scenario: Node.js App with Docker Compose Multiple Mounts

Service name:
  app

Image:
  node:22-alpine

Container name:
  node-compose-app

Mount 1 - Bind Mount:
Host path:
  /root/node-project/src

Container path:
  /app/src

Mode:
  read-write

Mount 2 - Bind Mount:
Host path:
  /root/node-project/config

Container path:
  /app/config

Mode:
  read-only

Mount 3 - Volume:
Volume name:
  node-uploads

Container path:
  /app/uploads

Mount 4 - tmpfs:
Container path:
  /app/cache

Environment:
  NODE_ENV=production
  PORT=3000

Port:
  3000:3000

Restart policy:
  unless-stopped

```
---
### کد هایه فایل copose.yaml
```
services:
  app:
    image: node:22-alpine
    container_name: node-compose-app

    volumes:
      - /root/node-project/src:/app/src:rw
      - /root/node-project/config:/app/config:ro
      - node-uploads:/app/uploads

    tmpfs:
      - /app/cache

    environment:
      - NODE_ENV=production
      - PORT=3000

    ports:
      - "3000:3000"

    restart: unless-stopped

volumes:
  node-uploads:
```
۱- `services:` → شروع تعریف سرویس‌ها  
۲- `app:` → اسم سرویس  
۳- `image:` → ایمیج کانتینر  
۴- `container_name:` → اسم کانتینر  
۵- `volumes:` → تعریف Bind Mount و Volume  
۶- `/host/path:/container/path` → Bind Mount  
۷- `volume-name:/container/path` → Named Volume  
۸- `:ro` → فقط خواندن  
۹- `:rw` → خواندن و نوشتن  
۱۰- `tmpfs:` → مسیر موقت داخل RAM  
۱۱- `/app/cache` → فقط مقصد داخل کانتینر  
۱۲- `environment:` → متغیرهای محیطی  
۱۳- `ports:` → اتصال پورت Host به Container  
۱۴- `restart:` → سیاست Restart  
۱۵- `volumes:` پایین فایل → تعریف Named Volume
