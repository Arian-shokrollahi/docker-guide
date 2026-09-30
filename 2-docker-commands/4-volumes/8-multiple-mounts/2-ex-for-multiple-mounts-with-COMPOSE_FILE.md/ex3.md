# این سری به دومدل میگم این فایل compose رو با مدل طولانی و کوتاه
---
## این خوده سناریو

```
Scenario: Advanced Multiple Mounts with Docker Compose

Service name:
  backend

Image:
  python:3.12-slim

Container name:
  backend-prod

Mount 1 - Bind Mount:
Host path:
  /root/backend/src

Container path:
  /app/src

Mode:
  read-write

Mount 2 - Bind Mount:
Host path:
  /root/backend/config

Container path:
  /app/config

Mode:
  read-only

Mount 3 - Volume:
Volume name:
  backend-uploads

Container path:
  /app/uploads

Mount 4 - Volume:
Volume name:
  backend-logs

Container path:
  /app/logs

Mount 5 - tmpfs:
Container path:
  /app/cache

tmpfs size:
  256MB

Environment:
  APP_ENV=production
  APP_PORT=5000
  DEBUG=false

Ports:
  5000:5000

Restart policy:
  unless-stopped

Command:
  python /app/src/app.py


```

---
### مدل ساده به صورت سینتکس هایه کوتاه

```
services:
  backend:
    image: python:3.12-slim
    container_name: backend-prod

    volumes:
      - /root/backend/src:/app/src:rw
      - /root/backend/config:/app/config:ro
      - backend-uploads:/app/uploads
      - backend-logs:/app/logs

    tmpfs:
      - /app/cache:size=256m

    environment:
      APP_ENV: production
      APP_PORT: "5000"
      DEBUG: "false"

    ports:
      - "5000:5000"

    restart: unless-stopped

volumes:
  backend-uploads:
  backend-logs:
```

---
## این با سینتکس طولانی

```
services:
  backend:
    image: python:3.12-slim
    container_name: backend-prod

    volumes:
      - type: bind
        source: /root/backend/src
        target: /app/src

      - type: bind
        source: /root/backend/config
        target: /app/config
        read_only: true

      - type: volume
        source: backend-uploads
        target: /app/uploads

      - type: volume
        source: backend-logs
        target: /app/logs

      - type: tmpfs
        target: /app/cache
        tmpfs:
          size: 268435456

    environment:
      APP_ENV: production
      APP_PORT: 5000
      DEBUG: "false"

    ports:
      - "5000:5000"

    restart: unless-stopped

    command: python /app/src/app.py

volumes:
  backend-uploads:
  backend-logs:
```

---
## مدل دوم خوانایی زیادی دارد ولی خیلی طولانی ولی پیشنهاد میکنم یاد بگیرید بدردتون میخورد
