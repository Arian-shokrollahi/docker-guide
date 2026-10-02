# یه کلید دیگه env_file که یکی از مهمترین کلید هاست بنظرم اگر با environment 
---
## اول چیست این compose key:
ا-`env_file` یک **Service-level key** در Docker Compose است که بهت اجازه می‌ده Environment Variableها رو از یک فایل جدا وارد Container کنی.

---
### چه کمکی میکند به شما و مشکل چیست چرا بهتره از env_file استفاده کنی به جایه environment
یکی از کاربردهای اصلی `env_file` همینه که Compose تمیزتر و خواناتر بمونه.

به‌جای این:

```
services:
  backend:
    environment:
      APP_ENV: production
      PORT: "5000"
      DB_HOST: db
      DB_PORT: "5432"
      DB_NAME: shop
      DB_USER: admin
      DB_PASSWORD: password
      LOG_LEVEL: info
```

می‌نویسی:

```
services:
  backend:
    env_file:
      - .env.production
```

و همه مقادیر میرن داخل:

```
APP_ENV=production
PORT=5000
DB_HOST=db
DB_PORT=5432
DB_NAME=shop
DB_USER=admin
DB_PASSWORD=password
LOG_LEVEL=info
```

پس برای جزوه‌ات می‌تونی بنویسی:

> ا-`env_file` باعث می‌شود متغیرهای محیطی را از Compose جدا کنیم؛ در نتیجه فایل Compose خلوت‌تر، خواناتر و مدیریت تنظیمات Dev/Prod راحت‌تر می‌شود.

فقط یادت باشه هدفش فقط «خلوت کردن» نیست؛ **جدا کردن configuration از ساختار Compose** هم هست.

---
## چیکار میکند برایه ما docker compose file

یعنی به‌جای اینکه این‌ها رو مستقیم داخل Compose بنویسی:

```
environment:
  APP_ENV: production
  DB_HOST: db
  DB_PORT: "5432"
  DB_NAME: shop
```

می‌تونی داخل یک فایل جدا بذاری:

```
APP_ENV=production
DB_HOST=db
DB_PORT=5432
DB_NAME=shop
```

و در Compose فقط بنویسی:

```
services:
  backend:
    env_file:
      - .env.production
```

### ساختار

```
services:
  SERVICE_NAME:
    env_file:
      - FILE_PATH
```

مثلاً:

```
services:
  backend:
    image: my-backend:v1
    env_file:
      - .env.production
```

ساختار پروژه:

```
project/
├── compose.yaml
├── .env.production
└── backend/
```

فایل `.env.production`:

```
APP_ENV=production
PORT=5000
DB_HOST=db
DB_PORT=5432
DB_NAME=shop_prod
DB_USER=shop_admin
```

این متغیرها وارد Environment داخل `backend` می‌شن.

---

### مثال واقعی

```
services:
  backend:
    image: my-backend:v1

    env_file:
      - .env.production

    ports:
      - "8000:5000"

  db:
    image: postgres:16
```

فایل:

```
APP_ENV=production
PORT=5000
DB_HOST=db
DB_PORT=5432
DB_NAME=shop_prod
DB_USER=shop_admin
DB_PASSWORD=mypassword
```

یعنی:

```
.env.production
      ↓
env_file
      ↓
backend container
      ↓
APP_ENV
PORT
DB_HOST
DB_PORT
...
```

### تفاوت `environment` و `env_file`

`environment`:

```
environment:
  APP_ENV: production
  PORT: "5000"
```

مقادیر رو مستقیم داخل Compose می‌نویسی.

`env_file`:

```
env_file:
  - .env.production
```

مقادیر رو از فایل جدا می‌خونی.

### می‌تونی هر دو رو با هم استفاده کنی

```
services:
  backend:
    env_file:
      - .env.production

    environment:
      LOG_LEVEL: warning
```

اگر یک متغیر هم در `env_file` باشه و هم در `environment`، مقدار داخل `environment` اولویت بالاتری داره.

مثلاً فایل:

```
LOG_LEVEL=info
```

و Compose:

```
environment:
  LOG_LEVEL: warning
```

داخل Container مقدار نهایی میشه:

```
LOG_LEVEL=warning
```

### فرق مهم `.env` و `env_file`

این دو تا رو قاطی نکن:

```
.env
```

معمولاً برای **Compose variable substitution** استفاده میشه.

مثلاً:

```
image: myapp:${TAG}
```

ولی:

```
env_file:
  - app.env
```

برای فرستادن Environment Variableها به داخل Container استفاده میشه.

پس برای جزوه:

> ا-`env_file` فایل Environment Variableها را می‌خواند و مقادیر آن را وارد Container مربوط به همان Service می‌کند.
