
# در محیط پروداکشنی و سروری چجوری متغیر میشه داد و ست کرد

در محیط Production معمولاً برای مدیریت متغیرهای محیطی، مخصوصاً متغیرهای حساس، از روش‌های حرفه‌ای‌تر استفاده می‌شود و بهتر است به `export` دستی در `.bashrc` یا نگهداری Secretها داخل فایل `.env` وابسته نباشیم.

روش‌های رایج‌تر در Production:

- `systemd EnvironmentFile`
    
- متغیرهای CI/CD
    
- Secret Manager
    
- Environment Variables سطح سرور
    

برای متغیرهای معمولی مثل:

```
APP_ENV
PORT
DB_HOST
LOG_LEVEL
```

می‌توان از Environment سرور، systemd یا CI/CD استفاده کرد.

برای متغیرهای حساس مثل:

```
DB_PASSWORD
API_KEY
JWT_SECRET
ACCESS_TOKEN
```

بهتر است از Secret Manager یا Secrets مربوط به CI/CD استفاده شود.

> نکته: فعلاً فقط باید بدانیم که در Production معمولاً متغیرها از یک منبع بیرونی به Docker Compose داده می‌شوند و Compose آن‌ها را به Container موردنیاز منتقل می‌کند. جزئیات systemd، CI/CD و Secret Manager را بعداً بررسی می‌کنیم.
